[Null Pointer Exceptions In Java - What EXACTLY They Are and How to Fix Them - YouTube](https://www.youtube.com/watch?v=lm72_HCd17s&list=WL&index=19&t=2s)


發生情境
使用 null  會發生
![[CleanShot 2024-12-14 at 08.32.35.png|825]]


null 可以塞到 Boolean -> check 也會發生
因此這邊建議用 boolean
![[CleanShot 2024-12-14 at 08.33.37.png]]
![[CleanShot 2024-12-14 at 08.34.17.png]]


for loop null
![[CleanShot 2024-12-14 at 08.34.27.png]]

因此建議塞 空 arr
![[CleanShot 2024-12-14 at 08.34.48.png]]



how to handle?

null check like below
![[CleanShot 2024-12-14 at 08.35.04.png]]

有時候的確很麻煩
![[CleanShot 2024-12-14 at 08.35.20.png]]


函數盡量不要return null
![[CleanShot 2024-12-14 at 08.36.48.png]]


return empty 那個 object
![[CleanShot 2024-12-14 at 08.36.03.png]]


這樣會噴，如果 myOtherCat is null
![[CleanShot 2024-12-14 at 08.36.59.png]]

可以反過來寫避免

![[CleanShot 2024-12-14 at 08.37.22.png]]