[(780) How to Think Like an Architect - Mark Richards - YouTube](https://www.youtube.com/watch?v=W7Krz__jJUg&list=WL&index=3)


談msg 要用key-based 還是 full-payload?

這是一個tradoff
- key-based
	- 好處包括:bandwith cost, 不需要處理版本(因為你會帶一堆東西了), 另外也不需要處理 stamp coupling(就是很多東西都沒用到), 最後也避免 data integrity 問題(你傳整包，傳的時候，如果 db 資料有變化，這樣兩個資料不同耶)
- full-payload
	- 好處: 好 scale, 因為不需要去 db, 可能 perf 也會比較好

![[IMG-How to Think Like an Architect - Mark Richards-20241202210310534.png|670]]

以下三點是架構思考需要有的


1. biz need <-> 軟體 capability
	- echo to architect elevator, [[Thinking Like an Architect by Gregor Hohpe#Connect levels]]
![[IMG-How to Think Like an Architect - Mark Richards-20241202210310641.png|694]]


2. 建立出技術的深度
![[CleanShot 2024-12-02 at 21.04.08.png|700]]


3. 一定有 tradeOff, 只是看你有沒有深度分析
![[CleanShot 2024-12-02 at 21.04.21.png|548]]


# 把 biz need -> 架構特性

要可以翻譯 from biz -> 技術
![[CleanShot 2024-12-02 at 21.05.10.png]]



![[CleanShot 2024-12-02 at 21.06.18.png]]


需要翻譯
![[CleanShot 2024-12-02 at 21.07.18.png|788]]


那到底我們需要哪些特性? 從下面這些地方去思考
1. biz domain
2. user story, req
3. discuss and engaging to biz ppl



![[CleanShot 2024-12-02 at 21.11.32.png]]


另外一個例子
time to market
- maintainablity
- testablity
- deployability


![[CleanShot 2024-12-02 at 21.16.45.png]]


![[CleanShot 2024-12-02 at 21.17.31.png]]


如果 scale 很重要，那你大概會選擇這三種
![[CleanShot 2024-12-02 at 21.23.06.png]]



如果 cost 和簡單很重要..
![[CleanShot 2024-12-02 at 21.24.15.png]]


可以來這裡下載
和對每一個特徵更細節的介紹
[Resources | Developer to Architect | Mark Richards](https://www.developertoarchitect.com/resources/)

![[CleanShot 2024-12-02 at 21.24.45.png]]



你不能沒有左邊作為一開始，然後就有右邊的架構
![[CleanShot 2024-12-02 at 21.28.55.png]]


# Technical Depth vs. Breadth


知識三角形
![[CleanShot 2024-12-02 at 21.32.50.png|780]]


game of life:
把最下面的東西移動到上面
how?
talk to ppl
go to conference
see new vid
![[CleanShot 2024-12-02 at 21.33.18.png]]
這樣哪天有需要，你就會知道...好像有這個可以試試看喔



when to move from "第二層" 到"第一層"?
這就是當你老闆說，下禮拜我們開始要用 zoo keeper
-> 這時候，你聽過, 但沒用過，你就可以開始學
![[CleanShot 2024-12-02 at 21.35.33.png]]




cautionaly tale:最上面你懂的，你要 maintain 才會 keep
不然又會變成下面
![[CleanShot 2024-12-02 at 21.37.05.png]]

![[CleanShot 2024-12-02 at 21.39.08.png]]



架構思考要花很多時間在中間層
![[CleanShot 2024-12-02 at 21.39.35.png]]



jr. 最上面比較多
![[CleanShot 2024-12-02 at 21.40.37.png]]


sr, 上面長更大
下面一樣很沒啥花時間學
![[CleanShot 2024-12-02 at 21.41.27.png]]


jr 架構 -> 開始經營中間層
![[CleanShot 2024-12-02 at 21.42.29.png|815]]


sr 架構師
中間層更大
![[CleanShot 2024-12-02 at 21.44.13.png|823]]




你要知道更多 tech, 才可以更好的找到最適合的 solution given current ctx


作者介紹幾個網站讓你探索
[InfoQ: Software Development News, Trends & Best Practices - InfoQ](www.infoq.com)
[Technology Radar | Guide to technology landscape | Thoughtworks](https://www.thoughtworks.com/radar)
[DZone: Programming & DevOps news, tutorials & tools](https://dzone.com/refcardz)
![[CleanShot 2024-12-02 at 21.54.15.png|662]]


打開 mail, 或是看 infoQ
找出 you don't know you don't know -> **找出那些從沒看過的東西** -> know what it is and when to use it or not

![[CleanShot 2024-12-02 at 21.57.37.png|548]]



一天 20 min
![[CleanShot 2024-12-03 at 09.23.05.png|576]]


# Analyzing Trade-offs

多數 開發者都只看到 benefit 但沒看到 tradeOff

![[CleanShot 2024-12-03 at 09.26.16.png|610]]



there is NO best practice for what architecture to use -> it all depends on your specific, current context, 100 % contextual



trade off 怎麼選?
看 biz need
![[CleanShot 2024-12-03 at 09.30.25.png|814]]



小心 out of ctx
![[CleanShot 2024-12-03 at 09.30.52.png]]



![[CleanShot 2024-12-03 at 09.31.15.png]]

![[CleanShot 2024-12-03 at 09.34.36.png]]

選 shared lib? no! 要看 ctx
![[CleanShot 2024-12-03 at 09.35.56.png|879]]



![[CleanShot 2024-12-03 at 09.36.59.png]]