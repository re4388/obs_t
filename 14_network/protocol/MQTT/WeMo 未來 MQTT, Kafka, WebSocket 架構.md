ref: [Bi-directional Communication - App/Box/Service](https://hackmd.io/cPXHm-8dTmK-ju94WSWy4A)

![[CleanShot 2024-11-06 at 19.54.29.png]]





- BB <-> mqtt <-> 微服務(s)
- useApp, serviceApp, Apollo, 透過 mqtt over websocket 來跟 微服務(s) 溝通 (一樣，中間有 broker)
- 微服務(s) 透過 kafka 來進行 持久性 msg pub-sub 的作業
![[IMG-WeMo 未來 MQTT, Kafka, WebSocket 架構-20241106193207532.png]]


![[IMG-WeMo 未來 MQTT, Kafka, WebSocket 架構-20241106193207654.png]]