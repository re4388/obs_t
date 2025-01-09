[[k3d]]


[Day 2 本機環境安裝 K3D & kubectl - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10319629)

```bash


# 啟動 k3s server => shall not use this in k3d case
# sudo k3s server start --write-kubeconfig-mode
# sudo k3s server start 


# k3s yaml change per to allow non-root user
# chmod 666 /etc/rancher/k3s/k3s.yaml



k cluster-info

# kubctrl to get nodes
kubectl get nodes
k get pods


# 建立方法
k run nginx-pod --image=nginx
# or
k apply -f <filename>


# list objects
kubectl get services # list all services in the current namespace (defined in config file or default)
kubectl get pods -A # list all pods in all namespace, -A = --all-namespaces
kubectl get pods -o wide # list all pods in the current namespace, with more details
kubectl get nodes -o wide # list all nodes with more details

# describe - view the details
kubectl describe pods my-pod
kubectl describe service mysql-service -n app # view the details of mysql-service service in app namespace

# delete
kubectl delete pod,service baz foo # delete pods and services with same names "baz" and "foo"

# view logs of pod
kubectl logs <pod name>


```