後一筆為 valid
	- beginAt 欄位, so 可以預排價格 
	- so 會傳入 beingAtParam as param and then 只會撈  beginAt and createAt 都要小於 beingAtParam （就是已經開始和已經建立）的 record 
	- and then sort by beginAt desc and then sort by Id desc and get the first time line 下的 pricingId
	- 一個時間點只有一個 pricingId 生效
- 因此編輯 pricing  就是新增一筆 pricing time line
- pricing rule 需要依照不同的身分去給定不同的 config/algo

![[IMG-pricing 主表 -> pricing timeline -> pricing config-20241105125915780.png]]



