

改用 dto


dto 的壞處
- 沒辦法像是 interface 可以 pick, mit, 復用比較低

但是好處是，可以封裝你的資料結構
封裝後，轉換資料可以不用混在 svc 層，把資料轉換讓 dto 那邊處理
你的 svc 層的 code 會更簡潔


diff
![[CleanShot 2024-11-06 at 16.50.30.png|585]]
![[CleanShot 2024-11-06 at 16.50.48.png|591]]


![[CleanShot 2024-11-06 at 16.50.03.png|859]]