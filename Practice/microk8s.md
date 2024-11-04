````
MICROK8S_VERSION=1.24; KUBE_VERSION=1.24.0
````
```
snap install microk8s --classic --channel=$MICROK8S_VERSION/stable
```
Проверка статуса:
```
microk8s status --wait-ready | grep -z "microk8s is running"
```
После возврата статуса, у нас запущен одноузловой кластер Kubernetes. Давайте проверим версию Kubernetes:

```
microk8s kubectl version --output=yaml
```
Правильный бинарный файл `kubectl`**:

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

Установим 

```sudo install -o root -g root -m 0755 kubectl /usr/bin/kubectl

kubectl version --client
```

Мы установим контекст `kubectl` в контекст `microk8s`:
```
microk8s config > $HOME/.kube/config
```
```
kubectl version && kubectl get nodes,namespaces
```
```
kubectl get --raw '/healthz?verbose' | grep ' ok'
```
```
kubectl get --raw /
```
```
kubectl get --raw /api/v1/namespaces/default | jq
```
```
systemctl list-units 'snap.microk8s.*' --no-pager
```
```
microk8s add-node
```
```
microk8s enable dashboard dns registry metrics-server
```
```
kubectl get pods --namespace kube-system
```
