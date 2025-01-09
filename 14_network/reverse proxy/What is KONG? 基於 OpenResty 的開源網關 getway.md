- [https://docs.konghq.com/gateway/latest/](https://docs.konghq.com/gateway/latest/)


- 基於 OpenResty 的開源網關。
    - OpenResty 是基於 Nginx 與 Lua
    - Ngnix 高性能， Lua 好擴充
        - 相對於 Ocelot 或 Gateway 這些類型的網關來說，Kong 的優勢是具有更高的性能



- 網關 api gateway 的功能包括：
    - service discovery
    - 權限控管
    - rate limit
    - ... 等
    
    
網關的主要功能，其實總結起來就兩個字 "統一"，無論是作爲應用的入口、認證授權、熔斷限流等等主要都是爲了統一的地方做一些事情

![[CleanShot 2024-11-03 at 19.18.42.png]]

- 主要依賴四個核心項目, 分別是 upstream、target、service、route
    - route
	    - **定義**：Route 是指將外部請求映射到特定 Service 的規則。每個 Route 定義了如何將請求路由到相應的 Service。
		- **用途**：Route 可以基於請求的 URL 路徑、HTTP 方法、標頭等進行匹配。這使得 Kong 能夠根據請求的特徵將其轉發到正確的 Service。
		- **示例**：你可以為 `user-service` 創建一個 Route，當請求的路徑為 `/users` 時，Kong 將該請求轉發到 `user-service`。
    - service
		- **定義**：Service 是指在 Kong 中定義的 API 服務。每個 Service 代表一個可以被外部客戶端訪問的 API，並且與一個 Upstream 相關聯。
		- **用途**：Service 定義了如何訪問後端服務，包括請求的路徑、協議（HTTP/HTTPS）和其他配置（如超時、重試等）。這使得 Kong 能夠將外部請求轉發到正確的 Upstream。
		- **示例**：你可以創建一個名為 `user-service` 的 Service，並將其指向相應的 Upstream，這樣外部客戶端就可以通過 Kong 訪問用戶服務。
    - upstream
	    - **定義**：Upstream 是指一組後端服務的集合，這些服務可以是微服務、應用程序或其他 API。Upstream 允許你將多個服務組織在一起，以便於管理和負載均衡。
		- **用途**：通過定義 Upstream，Kong 可以將請求路由到這些後端服務。這樣可以實現負載均衡、故障轉移和其他高可用性特性。
		- **示例**：假設你有一個電子商務應用，後端有多個服務（如用戶服務、產品服務和訂單服務），你可以為這些服務創建一個 Upstream。
	- target
		- **定義**：Target 是指 Upstream 中的具體後端服務實例。每個 Target 都包含一個具體的主機名或 IP 地址，以及可選的端口號。
		- **用途**：Target 允許 Kong 知道如何訪問 Upstream 中的具體服務實例。這樣，Kong 可以根據配置的負載均衡策略將請求發送到正確的 Target。
		- **示例**：如果你的 Upstream 包含一個用戶服務，則可以為該服務的不同實例（如 `user-service-1` 和 `user-service-2`）創建多個 Target。
![[CleanShot 2024-11-03 at 19.52.44.png]]