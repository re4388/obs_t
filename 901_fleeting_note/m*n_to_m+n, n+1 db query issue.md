---  
alias:  
creation_date: 2024-05-22 11:22  
tags: 
---


related: 
- [N+1 Queries | Sentry Documentation](https://docs.sentry.io/product/issues/issue-details/performance-issues/n-one-queries/)
- [[_db_idx]]

---  

[[wemo_code_note]]



# 問題

基本上就是一個 array of object
然後 object 裡面還有 array
```ts

const example = [
	{name: 'a1', addr: 'b2',  arr: [1,2,3,4]},
	{name: 'a2', addr: 'b3',  arr: [1,2,3,4]},
	{name: 'a3', addr: 'b4',  arr: [1,2,3,4]},
	{name: 'a4', addr: 'b2de', arr: [1,2,3,4]},
	{name: 'a5', addr: 'bde2', arr: [1,2,3,4]},
	{name: 'a6', addr: 'bde2', arr: [1,2,3,4]},
	{name: 'a7', addr: 'bde2', arr: [1,2,3,4]},
	// ..
]


```



第一版
這樣寫有DB n+1問題
order 如果有 100個
每一個 order 又有 order item 20 個
這樣一個 order 就 2000個 db query
![[IMG-m*n_to_m+n, n+1 db query issue-20241003104934023.png]]


第二版
上面先撈出orderItem, 一筆 db query, 解掉 n+1

但是下面為 m * n 
m is order lenth
n is orderItem length

有perf 進步空間

```ts hl:10,13

const orderItems = await this.db.orderItems.findByOption({
	select: condition.orderItems,
	where: {
	  orderId: In(orderIds)
	}
})

// 共 m * n
data = data.map((order) => { // <-- 這邊 O(n)
	return {
	  ...order,
	  orderItems: order.filter(order => order.id === orderItem.id) // 這邊 O(m)
	}
})

```


如果可以先把 order 這個 arr of obj 轉為 map
就可以實現 m + n, 如下

```ts


// 把 array of object 轉為 map
const orderIdToOrderItems = arrayToGroupedMap(orderItems, 'orderId') 

data = data.map((order) => { // <-- 這邊 O(n)
	return {
	  ...order,
	  orderItems: orderIdToOrderItems.get(order.id)  // <-- 這邊 O(1)
	}
})




export function arrayToGroupedMap<T extends Entity, P extends keyof T>(array: T[], key: P): Map<T[P], T[]> {

  const result = new Map<T[P], T[]>() // 建立一個 map

  array.forEach((entity) => {   // O(m)
    const k = entity[key] // 拿出你要當做 key 的那個 field
    
    // 如果還沒有塞入map, 就塞入
    if (!result.has(k)) {
      result.set(k, [])
    }
    
    // 把 entity 塞進去
    result.get(k)!.push(entity)
  })

  return result
}


```

arrayToGroupedMap
主要是把
input
```json

 data [
      {
        id: 'test orderid 1',
        amount: 10,
        userId: '1',
      },
      {
        id: 'test orderid 2',
        amount: 10,
        userId: '1',
      }
    ]

```

變成
```json

  map {
  
  'test orderid 1' -> {
    id: 'test orderid 1',
    amount: 10,
    userId: '1',
    },

  'test orderid 2' -> {
    id: 'test orderid 2',
    amount: 10,
    userId: '1',
   }
  
  }


```




---


# 另一個例子 in wemo
```ts hl:13,16,18



export const bindChargingDetails = async (rents: RentVO[]) => {
  const rentIds = R.pluck('id', rents)

  const chargingDetailsMappingList = await db.chargingDetail.findByRentIds(rentIds)

  // 先組合成 Map Object 再來處理每個 rent 的 mapping，好處是資料量大的時候可以降低程式複查度
  // loop in loop 會變成 O (nm)，但先處理好再來查詢 O (n+m)

  // 這裡轉為 map, O(n)
  const detailsGroupByRentIdMap = R.groupBy(R.prop('rentId'), chargingDetailsMappingList)

  
  return rents.map((rent) => { // O(m)
    // 這裡挑出 key, O(1)
    const rentChargingDetails = R.pluck('chargingDetail', detailsGroupByRentIdMap[rent.id] || [])

    rentChargingDetails.sort((a, b) => Number(b.id) - Number(a.id))
    const { mileageDetail } = classifyRentChargingDetails(rentChargingDetails)
    return {
      ...rent,
      mileageFee: mileageDetail?.amount || 0
    }
  })
}


```



# 另一個例子
```ts

const arrInArr = [
  { name: 'a1', zone: '1', arr: [1, 2, 3, 4] },
  { name: 'a2', zone: '2', arr: [5, 6, 7, 8] },
  { name: 'a3', zone: '3', arr: [9, 10, 11, 12] },
  { name: 'a3', zone: '1', arr: [9, 10, 11, 12] }
]

// 使用 groupBy and prop 可以把 array 轉為 map
// map 的 key 就是 R.prop 的參數
const arrInArrByAddrMap = R.groupBy(R.prop('zone'), arrInArr)
console.log('arrInArrByAddrMap -->', arrInArrByAddrMap)
// {
//   b2: [
//     {
//       name: "a1",
//       addr: "b2",
//       arr: [ 1, 2, 3, 4 ],
//     }, {
//       name: "a3",
//       addr: "b2",
//       arr: [ 9, 10, 11, 12 ],
//     }
//   ],
//   b3: [
//     {
//       name: "a2",
//       addr: "b3",
//       arr: [ 5, 6, 7, 8 ],
//     }
//   ],
//   b4: [
//     {
//       name: "a3",
//       addr: "b4",
//       arr: [ 9, 10, 11, 12 ],
//     }
//   ],
// }

const zoneIds = ['1', '2']

zoneIds.forEach((zoneId) => {
  // 然後你再用 pluck 出 map 中你要 access 的東西
  const rentChargingDetails = R.pluck('arr', arrInArrByAddrMap[zoneId] || [])
  console.log('rentChargingDetails -->', rentChargingDetails)
  // first loop
  // [
  //   [ 1, 2, 3, 4 ], [ 9, 10, 11, 12 ]
  // ]
  // second loop
  // [
  //   [ 5, 6, 7, 8 ]
  // ]
})


```



# 更多例子

原本寫法, nested for-loop, n *  m
```ts hl:2,3

regionalOperatorZones.forEach((zone) => {
  zone.polygons.forEach((polygon) => {
	const position = turfPoint([lng, lat])
	const targetPolygon = turfPolygon(polygon)
	if (booleanPointInPolygon(position, targetPolygon)) {
	  isInZone = true
	}
  })
})
```


改為 m+n
```ts hl:2,3,4

// group by m
const zonesByIdMap = R.groupBy((zone) => String(zone.id), regionalOperatorZones)
zoneIds.forEach((zoneId) => { // n here, hense m+n
  const polygons = R.pluck('polygons', zonesByIdMap[zoneId]).flat(2)  // 
  const position = turfPoint([lng, lat])
  const targetPolygon = turfPolygon(polygons)
  if (booleanPointInPolygon(position, targetPolygon)) {
	isInZone = true
  }
})


```



# 另一種玩法，用map 
```ts

      if (!R.isEmpty(regionalOperatorZones)) {
        const regionalOperatorZoneMap = new Map(regionalOperatorZones.map((zone) => [zone.zoneId, zone.regionalOperatorId]))

        layer.zones = layer.zones.map((zone) => {
          if (!R.isNil(zone.id)) {
            const regionalOperatorId = regionalOperatorZoneMap.get(zone.id)

            return {
              ...zone,
              regionalOperatorId
            }
          }

          return zone
        })
      }

```