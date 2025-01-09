---  
alias:  
creation_date: 2024-07-06 08:13  
tags: 
related:
- [[_software_design_idx]]

---  
- 跟 use 簡單函數帶arg的方式來抽象化的方式來比較的話，因為OOP 是最常見，也或許是比較好用的建立軟體架構的方式
	- 函數帶上 arg, even you can pass fn as arg, 抽象化的程度也不會 OOP 高
- 因為 OOP 可以加上 design pattern, 就可以建立出高度架構
	- (not saying fn alone can't do it, just not as much as resource as OOP has on design pattern)


但是因為架構和 design pattern 有自己的複雜度
因此一開始就建立這些東西，你是把程式加以限制的，另外也多了一些樣板code
因此，不是所有東西都要 OOP, 你不應該要 OOP 當你
- one shot
- 或是你無法判斷或是不覺得這個功能有可能從什麼角度去擴展 with design pattern
- 或是可以用 fn 角度去擴展就夠了

以上可能就不需要用 OOP + design pattwrn


---

用了之後

- 增加新的功能會很快 
	- 這如果是new dev 寫，還需要弄懂原本的架構，可能開發時間也不會快
	- So the real benefit is to 降低整體軟體複雜度 
		- 這邊是 in the long run
		- 符合上面應該要用 OOP 的情境
			- not one shot
			- code grow in a way you planned



如果你這個功能會需要被擴展
架設目前我有寫兩個 blokcer, 如果未來exntend to 8個，那複雜度只會長一點點
感覺類似現在複雜度是 4 -> 未來頂多就是 5

但是如果現在兩個blocker 都用plant fn 的方式寫，或許現在複雜度是 1,
但是當你長了8個，這8個就是不同 dev 寫，各自長，且沒有結構
那這8個的複雜度大概破10了，我們甚至可以說這8個沒啥關連，各自為政，複雜度會超級搞，情境會超級多

