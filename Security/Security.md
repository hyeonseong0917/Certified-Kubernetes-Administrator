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

Distributing Self-Signed CA Certificate
A client node may refuse to recognize a self-signed CA certificate as valid. For a non-production deployment, or for a deployment that runs behind a company firewall, you can distribute a self-signed CA certificate to all clients and refresh the local list for valid certificates.

On each client, perform the following operations:
```
$ sudo cp ca.crt /usr/local/share/ca-certificates/kubernetes.crt
sudo update-ca-certificates
```