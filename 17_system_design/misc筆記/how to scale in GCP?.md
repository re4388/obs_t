
ref: [Google Cloud Scalability - by Neo Kim](https://newsletter.systemdesign.one/p/google-cloud-scalability?utm_source=post-email-title&publication_id=1511845&post_id=147517391&utm_campaign=email-post-title&isFreemail=true&r=85bw7&triedRedirect=true)



- 如果你剛剛開始，用戶數量 < 1000
	- client -> cloud DNS(註一) -> VM(disk, DB, webapp)

![[CleanShot 2024-10-27 at 13.24.38.png]]




- 用戶數量變多 > 5000 -> VM loading too much, 偶而會 crash
	- 導入 [Managed Instance Group](https://cloud.google.com/compute/docs/instance-groups) (**MIG**) 
		- use auto scaler in MIG: 自動建立 vm if vm is not healthy
	- VM 前面放一個 LB
![[CleanShot 2024-10-27 at 13.27.19.png]]


- 用戶數量變多 > 10k 以上
	- 因為如果 VM crash, 裡面的 data and sql 也會一起出問題，依此拉出來
	- 另外 VM locate at different [zones](https://cloud.google.com/compute/docs/regions-zones)

![[CleanShot 2024-10-27 at 13.34.20.png]]


- 用戶數量變多 > 100K
	- 讓 sql 由 google 代管 -> see 註二
		- Ben note: 也不是無敵，缺點包括
			- 一些第三方的pg套件無法支援
			- 自己要控制好 cpu and ram
				- 但是提高後，降不回來
				- size 也是提高後，無法降
	- 加上一層 managed redis




- 用戶數量變多 > 1 M
	- 加上 global load balancer 註三
	- 加上 CDN



- 用戶數量變多 > 10 M and more
	- 單體 VM 服務 ->  micro service 架構 
	- 用 k8s 管理, GKE
	- 導入 pub sub
	- bigquery








註一:
Google Cloud DNS 提供的主要服務包括：
- 域名註冊: 雖然 Google Cloud DNS 主要用於託管 DNS，但它也提供域名註冊服務，讓你可以在 Google Domains 上註冊新域名。
- 域名託管: 這是 Cloud DNS 的核心功能。你可以將域名託管在 Google 的基礎架構上，並管理域名的 DNS 記錄。
- DNS 記錄管理: 你可以輕鬆地添加、修改和刪除各種 DNS 記錄，例如 A 記錄、AAAA 記錄、CNAME 記錄、MX 記錄、TXT 記錄、NS 記錄等。
- 全球 DNS 網絡: Google Cloud DNS 利用 Google 的全球網絡基礎架構，將你的 DNS 記錄分佈到全球各地的 DNS 伺服器，確保用戶可以快速可靠地訪問你的服務。
- 高可用性和冗餘性: Cloud DNS 具有高度的可用性和冗餘性，可以防止單點故障，確保你的 DNS 服務始終可用。
- 安全性: Cloud DNS 支援 DNSSEC (Domain Name System Security Extensions)，可以防止 DNS 欺騙和緩存污染等攻擊。
- 私有 DNS 託管: 除了公共 DNS 託管之外，Cloud DNS 還支援私有 DNS 託管，允許你在你的 Virtual Private Cloud (VPC) 網絡內託管 DNS 域名，並在 VPC 網絡之間共享這些域名。
- DNS 政策: Cloud DNS 允許你創建 DNS 政策，用於控制 DNS 查詢的路由和行為，例如基於地理位置的路由、流量導向等。
- 整合其他 GCP 服務: Cloud DNS 與其他 GCP 服務緊密集成，例如 Cloud Load Balancing、Kubernetes Engine 等，可以簡化應用程式的部署和管理。
- API 和命令行工具: Cloud DNS 提供了豐富的 API 和命令行工具，方便你以程式設計方式管理你的 DNS 記錄和配置。
- 監控和日誌記錄: Cloud DNS 提供了監控和日誌記錄功能，可以幫助你跟踪 DNS 查詢的性能和健康狀況。




註二：
- 自動化的資料庫管理： Cloud SQL 自動處理備份、複製、修補和高可用性，讓你無需擔心資料庫管理的繁瑣細節。
- 高可用性和災難復原： Cloud SQL 提供自動故障轉移和異地備份功能，確保你的資料庫在發生故障時仍可使用，並能快速從災難中復原。
- 安全性： Cloud SQL 提供多層安全防護，包括資料加密、網路存取控制和身份驗證，保護你的資料安全。
- 可擴展性： 你可以根據需要輕鬆調整 Cloud SQL 資料庫的大小和性能，以滿足應用程式的需求。
- 整合其他 GCP 服務： Cloud SQL 與其他 GCP 服務緊密集成，例如 Compute Engine、Kubernetes Engine、App Engine 和 Cloud Functions，方便你構建和部署應用程式。
- 多種資料庫引擎選擇： Cloud SQL 支援 MySQL、PostgreSQL 和 SQL Server，讓你選擇最適合你應用程式的資料庫引擎。
- 易於使用： Cloud SQL 提供了易於使用的控制台、命令行工具和 API，方便你管理你的資料庫。
- 監控和日誌記錄： Cloud SQL 提供了監控和日誌記錄功能，可以幫助你跟踪資料庫的性能和健康狀況。
- 遷移工具： Google 提供了工具，可以幫助你將現有的資料庫遷移到 Cloud SQL。





註三:
Google Cloud Global Load Balancing 提供的主要服務包括：
- 跨區域負載平衡： 將流量分佈到位於不同 GCP  region 的後端服務，即使其中一個區域發生故障，應用程式仍然可以繼續運行。
- 跨 continents 的負載平衡： 將流量分佈到位於不同 continents 的後端服務，為全球用戶提供更低的延遲和更好的性能。
- 單一 anycast IP： 使用單一 anycast IP 地址，將用戶流量導向到最近的後端服務，減少網路延遲。
	- Anycast 是一種網路尋址和路由方法，允許將同一個 IP 地址分配給位於不同地理位置的多個伺服器。當用戶向這個 anycast IP 地址發送請求時，網絡會自動將請求路由到地理位置最近的伺服器。
	- 因此可以設定單一 IP 位址，因此無需更新 DNS 原則
- 運行狀況檢查： 定期檢查後端服務的運行狀況，並自動將流量從不健康的實例導向到健康的實例。
- 故障轉移： 當一個區域或後端服務發生故障時，自動將流量轉移到其他健康的區域或後端服務。
- 流量管理： 提供多種流量管理策略，例如基於地理位置的路由、流量分割和加權負載平衡。
- 內容快取： 透過 Cloud CDN 整合，快取靜態內容，例如圖片、影片和網頁，進一步降低延遲並提高性能。
- HTTPS/SSL 卸載： 在負載平衡器上終止 HTTPS/SSL 連線，減輕後端服務的負擔，並提高安全性。
- WebSockets 支援： 支援 WebSockets 協定，允許建立實時雙向通訊應用程式。
- 整合其他 GCP 服務： 與其他 GCP 服務緊密集成，例如 Cloud DNS、Cloud CDN 和 Cloud Monitoring。



