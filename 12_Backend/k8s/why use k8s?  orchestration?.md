[Do you REALLY need Kubernetes? - YouTube](https://www.youtube.com/watch?v=z0HcYwYICBU&list=WL&index=3)



- LB: handle how req 可以依照某個規則走到每一個 node, pod
- svc discovery: 解決了 how do we keep track of freq changing IP of up and down 實例
- network: deal with how our svc are interconnected
- monitoring and loggin: deal with how we know what happen when things go wrong


然後如果你用 k8s, 你就獲得了一個一致的標準去處理上面這些事情
(this might be a good reason u use it even you current scale is not that big and you use managed svc)