---
aliases:
  - communication_idx
  - people_skill
---

related:
- [[溝通 with wife, family]]






Jack Wu
你要把對方當成不知道
一點一點的講
不要跳邏輯
不要突然橫空出示
不懂也要說不知道
不可以說錯的東西

# 工作溝通技巧
- 要從對方的角度去溝通
	- 常常停一下確認
		- 確認對方有 follow up
		- 確認我沒有走篇(很多時候，是我自己說到不是重點的地方，如果我停下來，對方其實可以讓我知道來改方向)
- [[在麥肯錫工作會發現一些有趣的事]]
- 盡量用單線結構
	ref: [](https://github.com/ruanyf/weekly/blob/master/docs/issue-288.md#%E6%8A%80%E6%9C%AF%E5%86%99%E4%BD%9C%E7%9A%84%E9%A6%96%E8%A6%81%E8%AF%80%E7%AA%8D)[https://github.com/ruanyf/weekly/blob/master/docs/issue-288.md#技术写作的首要诀窍](https://github.com/ruanyf/weekly/blob/master/docs/issue-288.md#%E6%8A%80%E6%9C%AF%E5%86%99%E4%BD%9C%E7%9A%84%E9%A6%96%E8%A6%81%E8%AF%80%E7%AA%8D)
	- 現實，不是單線的，很多線連接
	- 結論：要自己整理成單線
- 溝通技巧
	- **放慢語速，多點停頓**
	- **目的是消息互通，不是表示自己懂很多**
	- 可以換句話說對方說的，確認對方的意思有聽懂
	- 問對方是有聽懂和是否有問題
- 不要想到哪說到哪，說話要有一個邏輯的遞進的關係在
- 報告每一個 statement 簡單就好，更多資訊 indent
    - 方便閱讀者閱讀
- 講一個重點就好，不要太多

### 不要太多贅詞，會導致邏輯不清晰
from wemo jack
- 報告前，確認你要跟對方說的最重要的東西是什麼
- 如果你自己也不確定那對大家是否有幫助..
    - 應該很多情況是沒有幫助
    - 只會造成更多困惑
- 不要太多贅詞，會導致邏輯不清晰
- 標準要夠高
    - 要可以複現
    - 除非確定不用走
```md fold title:meeting_min


昨天
之前也有，還好

你說明事情

還車車廂 -> 因為講的太


整體太模糊


這邊會有 a,
因為 b
又因為 c 拿到 d 
但是如果 f, a 又是錯的

建議：
如果你自己最後自打嘴巴 -> 那你自己要釐清，假設說清楚



如果你自己也不確定 -> 那對大家是否有幫助 -> 應該很多情況是沒有幫助 -> 只會造成更多困惑

不然你就要跟大家說，我自己也沒有很確定。
可能有太多贅詞，導致邏輯不清晰。


缺少的
- 使用者沒關好
- 希望可以看到 box log

reproduce
實際去操作問題

目前沒有一個 mqtt 連線跨 be, app, si 的既有方法 (不是 sop) -> 

要花多少 effort 來解決


3分 
44個問題 -> 26個問題


3分
- 加分數? 請 sm 評估
- 如果無法加分?，那就是3分可以做的地步，但是要說清楚， study結果也要有很多保留詞

為何要加分數，也可以一半再說
然後說為何你要加分數
列出你要加的原因和風險


標準要夠高 -> 要可以複現 -> 除非確定不用走



需要更多的工作
  重現
  請SI看log


學會用更高的角度 -> 3d的視角


si一樣會遇到我的問題



我們還需要做什麼

- 抓幾個經典的 case? -> 抽出要測試的情境
- 目前資訊不夠
	- box 的 log -> 需要 SI 的協助
	- 需要確認車廂是否有關好
		- box-msg-update 打上來的可以相信嗎？ 有沒有可能打上來是開的，但實際卻是相反?
		- box 打給 system 的 event 跟實際的是否一致?
		- box 自己的 log 是否有記錄? 同上
- 我們還需要哪些事情, 需要多少 effort, 需要確定問題點在哪裡
	- 需要有實際上的 box, 車子，去做測試 




對了，bb 的版本也很重要，問題是否發生在特定版本上 hw/fw
塞車



溝通目標：
- 讓SM知道目前有這些還沒有做的工作 -> 只有 3 分，最多就做到這地步 -> 但其實還沒有達到 PM 預期的需求
- 需要另外補卡開卡
- pri? 我先不做 timeplan嗎？




簡單化 statement







==

think about 你要人家了解的是什麼？
不要太多，不要又是A又是B又是C, 
盡量有充足的資訊讓他相信A
數字
佐證

不要有太多反證

不要講太快
要停一下，問大家是否有沒有理解的


==
興趣

需求
想像有限


看看是否有即時的需求/問題
db migration
network
ci/cd
..
..
..
..
..
..


不要太分散

錄音





```





有效溝通一些想法
- think about how you can speak and think like jack
	- my obs
		- slow down, and think in step and speak in step
		- try to ensure each sentence you speak out is precise and other can understand
		- if you thoughts block (you are asking a Q you don't know how to ans)
			- write down and think what is the logical step/question you miss before you can answer that blocking question!!!
			- you skip some logical step and you need to also understand those step and then you can answer the block question

Don’t rush in mind. Slow down to think, find the thinking gap and you shall be capable to solve eventing.



[[rethink adam grant]]









[(592) 知道卻做不到，是因為自己笨嗎？硬核腦科學分析，揭秘自我成長的底層密碼！ - YouTube](https://www.youtube.com/watch?v=g6HSoShk9js&list=WL&index=18&t=331s)
- label the bad behavior you did before

- how?
	- think abt what bad behavior you did
	- label it and think abt how you shall encounter
	- next time, when this happen, you brain can more easily identify and stop urself
	- (since when this happen and not be labelled yet, the real_time_cognative_overload will not allow you to think clearly )

label 1: 好辯者
- not need to argument, if you're wrong and then you're wrong, no need to save your own face






[(583) 不傷人的溝通，最有品的說話方式 | 英文的 no judge 是這個意思 - YouTube](https://www.youtube.com/watch?v=WD_zZIT-uoY)
no judge
harry potter: 一個人的本性不是其固有特質，而是在於其選擇

應該是對事不對人


anti-example
你為何那麼懶
你為何總是出口傷人
xxx 總是 xxx
你為何那麼自私





- 生氣的時候不要說話 → 你一定會後悔
    - 欠思考就會說出對方不爽的話
    - 對方不爽
        - 對方一定不會依照你說的話做
        - 對方甚至會跟你另開戰場（根據你說錯的話）
    - 然後你說的話，根本沒有解決問題
    - 然後你說的話，還會製造新的問題



- 開會技巧
    會議的目標不是要讓大家同意我的觀點
    而是讓這個問題有個結論
    HOW?
    1. 了解會議室中每個人的觀點 。
    2. 做必要的事讓每個人都達到共識 。
    3. 即使會議結束完還沒共識，也不會強制要求一定要有結論。





## 如何說服別人
- 專心聽
	- listen carefully and totally understand what other said
	- 不要急，用更多心力專心聽
	- LISTEN → 要專心聽到位，你才可以真正思考到位，也才可以比較有能力說到位
- you can even help to summrize their points if that’s how commnication goes
- after this, then you bring our your argument
- why?
	- you need to let other believe that you’are a reliable ppl who are willing to listen to them and they can cooperate with you

- also see  [[rethink adam grant]]


## 溝通技巧
### 努力達到 50 50 的溝通頻率
- [Conversation Skills Essentials – Tynan.com](https://tynan.com/letstalk/)
	- 不要打斷
	- 接受回應，不要尋求回應。
		- 你說笑話，對方沒笑，你如果目的是尋求，那你就會不爽。但這是對方的自由。
	- 要用方法去拿到聽眾目前的興趣，來決定你目前要怎麼講。
	- 拒絕對方的話語，要說的很正面。
	- 如果對方說的話，讓你改變了心意，跟對方說，這是肯定對方。
	- 努力達到 50 50 的溝通頻率。
		- 如果對方一直講，那你要禮貌的打斷，多問。
		- 如果你一直講，喔…不要這樣做！！
	- benchmarking
		- if you get “Nice” “Cool” “Ok” this kind of response, means the talks is not engaging
		- try to go depth if you can to let both party to learn something from each other



## On leadership 
- 照顧感/monami style leader/begin this practice at home




# to see
[[How to Speak]]
