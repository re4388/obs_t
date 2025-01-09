related
- [Postgres Replication to kafka connect](https://hackmd.io/2Yq4sDK7QiuVadfdjcFSzw)

# code example
- [GitHub - re4388/kafka-nodejs-tutorial: A sample application using Kafka, Node.js and Docker](https://github.com/re4388/kafka-nodejs-tutorial)

## 用例


不受控制的微服務互相依賴, like below
![[IMG-kafka_intro-20241106184859031.png|637]]


為了解決服務到服務和持久性儲存排列日益複雜的問題，他們選擇開發一個可以作為事實來源的單一平台
這個平台，要可以作為公司內部的中樞神經系統 - 資料流向、處理 / 轉換並被其他下游系統（資料倉儲、索引、微服務等）使用的地方
因此，它的要求就是要可以負載大吞吐量（每秒數百萬條訊息），同時持久儲存大量資料（TB）
![[IMG-kafka_intro-20241106184859189.png|677]]


## patition
系統中的資料儲存在 topic 
主題的基本資料結構是 log, 一種按順序儲存記錄的簡單有序資料結構。
![[IMG-kafka_intro-20241106184859317.png]]

它是不可變的，並且具有 O (1) 寫入和讀取（只要它們來自尾部或頭部）。
因此，日誌越大，存取資料的速度就不會降低，這對於並發讀取來說非常有效率。

日誌的主要好處（也許也是 Kafka 選擇它的主要原因）是因為它針對 HDD 進行了最佳化。
HDD 在線性讀取和寫入方面非常高效，並且由於日誌的結構 - 線性讀取 / 寫入是您對其執行的主要操作
[正如我們在 S3 文章中所述](https://highscalability.com/behind-aws-s3s-massive-scale/)，自誕生以來，HDD 每位元組的價格已經便宜了 6,000,000,000 倍（經通貨膨脹調整）。 
Kafka 的架構針對經濟高效的本地部署系統進行了最佳化，該系統儲存大量數據，同時效能也非常出色！

PS: OS 有進行很多 線性操作的優化。 see [原文](https://highscalability.com/untitled-2/) 

## 如何達到高吞吐量?

**Parallelism 並行處理** 
- 越多 partition  + 越多 consumer , 吞吐量越高
	- but 使用更多記憶體，增加不可用時間，更多 open file handler
- partition的數量跟你想要消費的吞吐量有關
- 如果你要10g 的吞吐量，如果一個broker的負載有2g, 那你要5個 brorker，考慮 共兩個replica, 需要10個 broker
- msg 寫入某個partition後，還會 replicate 到其他幾個 node -> 高可用
![[IMG-kafka_intro-20241106184859447.png|896]]

![[IMG-kafka_intro-20241106184859531.png]]
- **批次處理 (Batch)** -> 減少網路傳輸次數，每一次都會有固定的 overhead
	- 首先 Kafka 將多個訊息打包成批次進行傳輸，而不是單個傳輸訊息。
	- 批次處理可以減少網路開銷，提高吞吐量。
	- batch send 有參數可以設定。
- **順序寫入磁碟 (Sequential Disk Writes)**
	- 接著，kafka 寫入磁碟的方式是順序寫入 sequential acess。
	- append -> O(1)
	- append 在 file system的 log and then 讓 OS handle
	- 不會用 jvm 的 gc, 也不自己 handle flush to disk
- **零拷貝 (Zero-Copy)**  -> 減少了在作業系統核心空間和用戶空間之間複製數據的次數
	- 用 zero copy 的方式處理，就是 producer 寫入的 data 進入 os buffer 後，直接可以傳到 NIC 的 buffer 然後 consumer 就可以從那邊讀了。 
		- 所以變成：OS buffer -> NIC buffer -> kafka Consumer
		- 傳統的作法是： OS buffer -> disk -> NIC buffer -> Socket buffer -> kafka consumer
	- 不過[這篇文章](https://highscalability.com/untitled-2/)有說，這不會是主要原因，因為這優化是降低 cpu loading, 但 CPU 很少成為優化良好的 Kafka 部署的瓶頸。另外加密和 SSL/TLS（所有生產部署的必備條件）已經禁止 Kafka 使用零拷貝，因為它會修改整個路徑中的訊息
	- ref: 
		- [Apache Kafka and the File System | Confluent Documentation](https://docs.confluent.io/kafka/design/file-system-constant-time.html#file-system)
		- [notion zero copy](https://www.notion.so/nture4388/30-05-I-O-Zero-Copy-iT-IT-09f125721d6242c5aa40bd4ed555f338?pvs=4)
		- [Kafka设计解析（六）：Kafka高性能关键技术解析](https://www.infoq.cn/article/kafka-analysis-part-6)
- **壓縮 (Compression):** -> 減少訊息大小，可以傳更多
	- Kafka 支援多種壓縮算法，例如 Gzip 和 Snappy。壓縮可以減少訊息的大小，提高網路傳輸效率，進而提高吞吐量。
- **offset 設計**讓 broker 負擔很低
	- 消息被傳到 partition 會被分配一個編號, 這個編號就是offset
	- consumer 需要的是 topic and offset 來 pull 要消費的消息 (kafka 主要是 pull model)
	- 消費者負責維護自己的消費偏移量 (offset)。這種簡單的設計減少了 Kafka broker 的負擔，提高了整體性能。
	- pull based, 就是 consumer 自己去拉 msg



---




- 平台(因為還有很多可以互相整合在一起的組件, like schema, kafka connect, kqlDB..etc)
- producer
	- 負載平衡可以是隨機的，分區函數可以自定義，也可指定一個用於分區的鍵，Kafka 使用該鍵來哈希到分區。
- use case:
	- 如日誌收集、事件追蹤和數據分析
	- 適合需要高吞吐量和低延遲
	- 適合需要長時間保存消息的場景（如事件溯源）






[Kafka APIs | Confluent Documentation](https://docs.confluent.io/kafka/kafka-apis.html#)
以下都有自己的API
- 生產者 → 把流寫入一個或多個主題
- 消費者 → 訂閱一個或多個主題來處理流
- 連接器  → 建立和 import/export 流 連接器
- Admin → 建立和管理客戶端，broker 和其他物件
- 流 → 和微服務溝通，input and output 的 api



## why need Schema Registry?
- 統一管理，確保資料結構一致
- 驗證和檢查 → 不用寫code
- 版本管理和檢查
- 資料序列化→ 不用寫code




## 如何解決消息積壓的問題?
ref: [盘点一下我用 kafka 两年以来踩过的一些非比寻常的坑 - 知乎](https://zhuanlan.zhihu.com/p/354140197)

脈絡

消息積壓指的是訊息在 Kafka 的主題 (topic) 中累積，但尚未被消費者 (consumer) 消費的情況。  這就像生產速度超過消費速度，導致訊息在佇列中排隊等待處理。

消息積壓的原因：
* 消費者消費速度慢： 消費者的處理能力不足，無法跟上生產者的生產速度。這可能是由於消費者程式碼效率低下、資源不足或其他問題導致的。
* 生產者生產速度過快：  生產者產生訊息的速度超過了消費者的處理能力。例如，突發流量或系統異常可能導致生產速度激增。
* Kafka 叢集資源不足： Kafka 叢集本身的資源，例如網路頻寬、磁碟 I/O 或 CPU，不足以處理訊息的流量。
* 消費者故障：  如果一個或多個消費者發生故障，它們將無法消費訊息，導致訊息積壓。
* 軟體錯誤： 消費者程式碼中的錯誤或 Kafka 叢集的配置錯誤也可能導致訊息積壓。

消息積壓的後果：
* 延遲增加： 訊息需要等待更長時間才能被消費，導致系統延遲增加。
* 性能下降： 訊息積壓會消耗 Kafka 叢集的資源，例如磁碟空間和網路頻寬，導致系統性能下降。
* 數據丟失：  如果訊息積壓超過了 Kafka 的儲存容量，可能會導致數據丟失。
* 應用程式故障：  嚴重的訊息積壓可能導致應用程式崩潰或無法正常工作。


如何避免和解決消息積壓：
* 監控 Kafka 叢集和消費者：  密切監控 Kafka 叢集的性能指標和消費者的消費速度，以便及早發現和解決潛在的訊息積壓問題。
* 提高消費者消費速度：  優化消費者程式碼，提高消費者的處理效率。可以考慮增加消費者數量、使用更高效的程式語言或算法，或者升級硬體資源。
* 控制生產者生產速度：  如果可能的話，控制生產者產生訊息的速度，避免生產速度過快。
* 擴展 Kafka 叢集：  如果 Kafka 叢集資源不足，可以考慮增加 broker 節點或升級硬體資源。
* 使用更有效的壓縮算法：  使用更有效的壓縮算法可以減少訊息的大小，提高網路傳輸效率，從而提高消費速度。
* 設計合理的重試機制：  在消費者發生故障時，設計合理的重試機制，避免訊息丟失。


文章提到的解決辦法：
- 精简发送的报文，减少网络传输
    - 類似沒必要傳一大堆payload, 只需要傳id, 然後再用 id 去 db 拉資料
- 检查 partition 是否有积压，尽量保持每个 partition 均衡
    - 設定 partition 積壓的 monitor
    - key 由 商戶 key → 訂單編號
- 检查数据库性能是否满足要求，可能有大表。
    - 大表導致消費者 process 太久，可能只需要放最近一年的，剩下都可以 archive


# kafka 因為量太大時，造成消息傳遞延遲，如何解決？

### 可能導致延遲的情況

1. **Broker 負載過高**：
    - 當 Kafka broker 接收的消息數量超過其處理能力時，可能會導致消息排隊和延遲。
2. **消費者速度慢**：
    - 如果消費者的處理速度低於生產者的發送速度，會導致消息在 Kafka 中積壓，從而增加延遲。
3. **網絡瓶頸**：
    - 如果生產者和 broker 之間的網絡帶寬不足，或者 broker 之間的網絡連接不穩定，可能會導致消息傳輸延遲。
4. **消息大小**：
    - 大型消息需要更多的時間進行傳輸和處理，可能導致延遲。
5. **副本同步延遲**：
    - 當使用副本（replication）時，若副本之間的同步速度過慢，會導致主副本無法快速確認消息，從而影響消息的可用性。
6. **磁碟 I/O 性能**：
    - Kafka 依賴磁碟 I/O 來存儲消息，如果磁碟性能不佳，會影響消息的寫入和讀取速度。
7. **配置不當**：
    - Kafka 的某些配置（如緩衝區大小、批量大小等）如果設置不當，可能會影響性能。

### 解決方案

1. **擴展 Broker**：
    - 增加更多的 Kafka broker 實例，以分散負載，減少單個 broker 的壓力。
2. **增加消費者數量**：
    - 增加消費者的數量，以提高消費速度。確保消費者能夠平行處理消息，從而減少消息的積壓。
3. **調整生產者配置**：
    - 調整生產者的緩衝區大小和批量大小，以提高發送效率。使用異步發送模式可以減少延遲。
4. **優化網絡配置**：
    - 確保生產者和 broker 之間的網絡連接穩定，並考慮使用更高帶寬的網絡。
5. **優化磁碟性能**：
    - 使用高效能的磁碟（如 SSD）來提高 I/O 性能，並確保磁碟有足夠的空間。
6. **監控和調整**：
    - 使用 Kafka 監控工具（如 Kafka Manager、Confluent Control Center 等）來監控 broker 的性能指標，根據需要進行調整。
7. **配置副本策略**：
    - 調整副本的配置，確保副本之間的同步速度足夠快。可以考慮使用更高的副本因子來提高可用性，但需注意平衡性能和資源使用。
8. **消息壓縮**：
    - 使用消息壓縮（如 Gzip、Snappy 等）來減少消息大小，從而提高傳輸效率。


## other note

- [Kafka - Data & BE Data Pipelines - HackMD](https://hackmd.io/cosVLzw3Tf-BMBDv7xiDSw)  by Jack
- [notion note](https://www.notion.so/nture4388/wemo-kafka-12c9df69750f80a387f3cec7fcf28f0e?pvs=4)
- 童書: [https://www.gentlydownthe.stream/](https://www.gentlydownthe.stream/)


ref:
- [Kafka 101](https://highscalability.com/untitled-2/)
- [topic为什么要进行分区? - 知乎](https://www.zhihu.com/question/28925721/answer/103094894)
 - [Kafka 之从 0 到 1 (part1) - ShuSheng007](https://shusheng007.top/2021/09/25/kafka-overview/)
 - [消息中间件方案选型 | 深入架构原理与落地实践](https://www.thebyte.com.cn/MessageQueue/mq-diff.html#apache-kafka)
 - [Apache Kafka 介紹. Data really powers everything that we… | by Chi-Hsuan Huang | Medium](https://medium.com/@chihsuan/introduction-to-apache-kafka-1cae693aa85e)
 - [(303) Martin Kleppmann | Kafka Summit London 2019 Keynote | Is Kafka a Database? - YouTube](https://www.youtube.com/watch?v=BuE6JvQE_CY)
 - [Publishing with Apache Kafka at The New York Times](https://open.nytimes.com/publishing-with-apache-kafka-at-the-new-york-times-7f0e3b7d2077)
 - [Using Kafka Streams API for predictive budgeting](https://medium.com/pinterest-engineering/using-kafka-streams-api-for-predictive-budgeting-9f58d206c996) — Pinterest
 - [LINE uses Apache Kafka](https://engineering.linecorp.com/en/blog/detail/80)
 - [再谈卡夫卡Kafka - 知乎](https://zhuanlan.zhihu.com/p/79810966)
 - [什么是 Kafka? 大系统没有它就有些麻烦了 - 知乎](https://zhuanlan.zhihu.com/p/77564114)
 - [(602) System Design: Why is Kafka fast? - YouTube](https://www.youtube.com/watch?v=UNUz1-msbOM)
 - [聊聊 Apache Kafka - 卡哥小技倆 Carger Tips](https://carger.tips/%E8%81%8A%E8%81%8A-apache-kafka#i)
 - 官網 [Apache Kafka](https://kafka.apache.org/)