
[(795) Software Architecture: The Hard Parts - Neal Ford - YouTube](https://www.youtube.com/watch?v=Q6RfMmMwhvM&list=WL&index=19)


# 一個微服務要怎麼切?
可以從下面幾點考慮

先看拆分的因素
- 是否不同功能, domain
- 這部份是否容易變動
- 這部份是否有不同的 operational architecture charateratic, like scake and throughput
- 這部份是否有不同的 fault tolerance 特性
- 這部份是否有不同的 access restrcition
![[CleanShot 2024-12-07 at 19.58.56.png]]


看應該整合的因素
- 是否會出現 db 交易？ 是否可以放一起?
- 資料是否需要互相拿才可以？ 是否可以放一起?
- 跨服務的 work flow 不管是 指揮還是編舞，都很複雜，如果放一起呢?
![[CleanShot 2024-12-07 at 20.04.23.png]]



下面講很多 service 共用一個 db 的問題
![[CleanShot 2024-12-07 at 20.10.13.png]]

# 分析技巧：trade-off分析一定要加上 biz ctx

![[CleanShot 2024-12-07 at 20.15.19.png]]



![[CleanShot 2024-12-07 at 20.15.59.png|770]]



不是看哪個勾比較多就選那個，還要看脈絡
![[CleanShot 2024-12-07 at 21.03.26.png|816]]


要考慮脈絡
我們要用很多不同的語言，因此要管理很多不同語言都共用同一個 shared lib -> 不容易
![[CleanShot 2024-12-07 at 21.05.06.png|821]]




# 分析技巧：trade-off分析要結合 biz care 的 use case
![[CleanShot 2024-12-08 at 11.39.40.png|613]]




![[CleanShot 2024-12-08 at 11.40.35.png|694]]





列出 use case 來分析 trade off
1 -> favor 拆分不同 svc, 你只需要改一個 svc
2 -> favor 拆分不同 svc, 你只需要加一個 svc
3 -> favor 放一起，不然你有跨 svc tx 要處理
![[CleanShot 2024-12-08 at 11.46.31.png]]



那個 case 至關重要?
這需要去跟 PM 討論 
btw, 用 pm 的語言，不是用架構特性去他們聊
![[CleanShot 2024-12-08 at 11.48.07.png]]



# 分析技巧：要比較類似的東西
![[CleanShot 2024-12-08 at 11.48.52.png|491]]



列出 solution 可以考慮列出來的 list 要 mece
![[CleanShot 2024-12-08 at 11.49.27.png|522]]


message queue 是 esb 的一部分，這樣比不對
![[CleanShot 2024-12-08 at 11.50.28.png|781]]


這樣比還比較合理
![[CleanShot 2024-12-08 at 11.51.42.png|989]]


# 分析技巧：不要過度偏好某一個技術解法
![[CleanShot 2024-12-08 at 11.52.28.png]]



我想要都用 broadcast msg 來處理 message
![[CleanShot 2024-12-08 at 11.53.11.png]]


因為如果不用broadcast msg, 我如果多一個接受者，我要改很多東西
我要建立新的 contract, queue, 寫 new receiver code
![[CleanShot 2024-12-08 at 11.53.43.png]]


但是如果你"只用broadcast msg" 那...那下面情況你無法處理
![[CleanShot 2024-12-08 at 11.57.45.png|699]]


比較
![[CleanShot 2024-12-08 at 11.58.45.png]]


哪一個重要
![[CleanShot 2024-12-08 at 11.59.09.png]]


# 分析技巧：找出 bottom line 溝通
![[CleanShot 2024-12-08 at 11.59.33.png]]



![[CleanShot 2024-12-08 at 12.00.02.png]]


![[CleanShot 2024-12-08 at 12.11.43.png]]

![[CleanShot 2024-12-08 at 12.12.07.png]]




# 分析技巧：定性分析
![[CleanShot 2024-12-08 at 12.14.44.png|618]]



## tx saga
saga這三個要一起看
![[CleanShot 2024-12-08 at 12.16.46.png|418]]

共有2的三次方，八種可能
![[CleanShot 2024-12-08 at 12.17.30.png|695]]

![[CleanShot 2024-12-08 at 12.17.47.png]]

都有不同的屬性
![[CleanShot 2024-12-08 at 12.18.40.png]]


![[CleanShot 2024-12-08 at 12.20.22.png]]


不是指揮造成，是指揮 + atomic 一致 導致那些紅點
![[CleanShot 2024-12-08 at 12.21.56.png]]




## saga 小節
- 如果本身的業務邏輯就很複雜，指揮至少可以幫你集中這些複雜性在同一個地方
- 編舞適合在你的業務邏輯本身就是分屬於不同的 部屬單元 上
![[CleanShot 2024-12-08 at 12.23.17.png|685]]




