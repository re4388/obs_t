

設計思維要進化

原本：
遇到這種 撈 多筆，跨 table, 我都習慣用 in,  一個 db 一個 query and no join
perf 考慮
缺點要知道是什麼 -> 程式比較複雜

更好的思維：
想一下你上面的 perf gain 是什麼
如果會重複資料 -> 用上面
如果可能會多 query，想要減少 query, 會 n+1 -> 用上面
但是如果都沒用，那就用簡單的 join 就好


**設計決策上，**
**你還要是花時間去思考這個業務用例的量會變多變少，或是這個功能未來是否會成為瓶頸**
**然後決定要如何 impl**
**而不是盡量都 impl 比較高效的作法(因為高效的作法通常都伴隨著程式碼的複雜度提高)**

另外 sort 也是
Jack prefer 說，如果你不確定量，讓 db sort 就好，簡單很多。
但是如果你確定量會變大，也不單單只要去思考就放到 pod/app層 就可以搞定
如果你的資料 1000千萬的筆，那你pod不是一樣會爆掉嗎?
那是不是變成要設定 limit, offset, 做 pagination, 


譬如，如果量不大，那你為何要寫比較複雜的 code to use applicaiton join
而不是讓 db 幫你 sort 好

下面讓 db 寫，就是那麼簡單
然後下面可能在加上 sort 邏輯
```sql

select * 
from vehilce_tag  as vt
join tag_action as ta on ta.tagId = vt.tag_id
where pricingId in (pricingIds)


// or

select *
from vehicle_tag as vt
where vt.tagId in (
  	 select ta.tagId
	 from tag_action as ta
	 where ta.pricingId in (pricingIds)
)

```


因此用 db 撈的邏輯簡單很多

但是如果你要用 code 會複雜很多

好處是。
```ts fold

  async getVehicleTagsByPricingIds(payload: GetVehicleTagsByPricingIdsRequest): Promise<PricingIdAndVehicleTags[]> {
    const tag = this.tag + '/getVehicleTagsByPricingIdsResponse'
    this.logger.debug({ tag, payload })

    let tagActions: TagAction[] = []
    const vehicleTags = await this.tagActionRepository.manager.transaction(async (manager) => {
      tagActions = await this.tagActionRepository.getTagIdByPricingIds(payload.pricingIds, {
        select: ['tagId', 'pricingId'],
        trans: manager
      })

      if (R.isEmpty(tagActions)) {
        throw new RpcException({
          code: status.NOT_FOUND,
          message: `tagActions not found`
        })
      }

      const tagIds = R.pluck('tagId', tagActions)
      return await this.vehicleTagRepo.getByTagIds(tagIds, { trans: manager })
    })

    const vehicleTagsByTagId = R.groupBy(R.prop('tagId'), vehicleTags)

    const res = tagActions
      .map((tagAction) => {
		// 一個 tagId 會不會有很多重複的 vechileTags?
        const { tagId, pricingId } = tagAction
        const vehicleTags = vehicleTagsByTagId[tagId]
        return {
          pricingId,
          vehicleTags: vehicleTags
            .map((vt) => ({
              id: vt.id,
              tenantId: vt.tenantId,
              tagId: vt.tagId,
              vehicleId: vt.vehicleId,
              rentFlow: vt.rentFlow,
              enabledAt: vt.enabledAt.toISOString(),
              createdAt: vt.createdAt.toISOString(),
              updatedAt: vt.updatedAt.toISOString(),
              disabledAt: vt.disabledAt ? vt.disabledAt.toISOString() : null
            }))
            .sort((a: { id: string }, b: { id: string }) => b.id.localeCompare(a.id))
        }
      })
      .sort((a: { pricingId: string }, b: { pricingId: string }) => b.pricingId.localeCompare(a.pricingId))

    return res
  }

```



![[CleanShot 2024-10-31 at 11.05.39.png]]


