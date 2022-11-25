# Kubernetes Security
## 1. View Certificate details
1. kube-api server Pod의 certificate 파일을 확인하는 방법
```
$ cat /etc/kubernetes/manifests/kube-apiserver.yaml 
```
--tls-cert-file에 있는 경로를 확인한다.
<br></br>
![default](./image/1123-1.PNG)
<br></br>
/etc/kubernetes/pki/apiserver.crt임을 확인할 수 있다.

2. ETCD server Pod의 ETCD server를 hosting하기 위한 Certificate 파일을 확인하는 방법
```
$ cat /etc/kubernetes/manifests/etcd.yaml 
```
--cert-file의 경로를 확인한다.
<br></br>
![default](./image/1123-2.PNG)
<br></br>
/etc/kubernetes/pki/etcd/server.crt임을 확인할 수 있다.

## 2. kubeconfig
kubeconfig 파일이란
- kubernetes의 설정 파일이며 클러스터의 apiserver에 접근할 때 사용할 인증 관련 정보를 가지고 있음.
- 클러스터, 사용자, 네임스페이스 및 인증 매커니즘에 대한 정보를 관리하는 파일로 클러스터에 대한 접근을 구성하는데 사용, 구성 파일을 참조하는 일반적인 방법
1. default kubeconfig 파일의 위치
```
$ controlplane ~/.kube ➜  ls
cache  config
```
/root/.kube/디렉토리에 위치한 config 파일이다.

2. /root/my-kube-config 안에 있는 "research" context로 전환하고 확인하는 방법
전환
<br></br>
kubectl config --kubeconfig={내 kubeconfig 파일의 위치} use context {전환하고 싶은 context}
```
$ controlplane ~ ➜ kubectl config --kubeconfig=/root/my-kube-config use-context research

```
확인
<br></br>
kubectl config --kubeconfig={내 kubeconfig 파일의 위치} current-context
