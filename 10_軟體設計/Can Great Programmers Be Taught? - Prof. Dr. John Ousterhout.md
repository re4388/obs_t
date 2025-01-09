ref: [Big Techday 24: Can Great Programmers Be Taught? - Prof. Dr. John Ousterhout - YouTube](https://www.youtube.com/watch?v=fPIuFo9V3Lk&t=2251s)



# CS190
write -> get feedback -> rewrite -> continue
![[IMG-Can Great Programmers Be Taught? - Prof. Dr. John Ousterhout-20241204132245344.png]]

# 重要概念
![[CleanShot 2024-12-04 at 13.22.49.png]]


# class should be deep
從此用這個 class/method 的觀念的成本和收益

low cost and high benefit, 所以interface 不要複雜，但是裡面可以幫忙做很多事情
![[CleanShot 2024-12-04 at 13.24.35.png|707]]

反例
![[CleanShot 2024-12-04 at 13.24.48.png|662]]


長短不是問題，重點是符合抽象
另外也應該要考慮上面提到的 deep class
![[CleanShot 2024-12-04 at 13.25.03.png]]



一開始設計如果不思考的general 一點，你的 code become more complicate and entaglement -> hard to maintain
![[CleanShot 2024-12-04 at 13.25.36.png]]


例子
右邊的抽象反而也沒有跟底層的邏輯綁在一起
左邊太多 info leak, 跟底層邏輯綁在一起
echo to [[Build Abstractions Not Illusions by Gregor Hohpe#如果你的抽象使用的詞彙跟你的東西是同一個world, level, 那就不是一個好的抽象]]
![[CleanShot 2024-12-04 at 13.26.15.png]]



不要先做不需要的功能
但是抽象 API 的設計要夠一般
![[CleanShot 2024-12-04 at 13.28.54.png]]



to watch
看到 27:24
[Big Techday 24: Can Great Programmers Be Taught? - Prof. Dr. John Ousterhout - YouTube](https://www.youtube.com/watch?v=fPIuFo9V3Lk&t=2251s)