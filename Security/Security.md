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

## 3. Role Based Access Control
1. dev-user라는 user에게 list,create,delete 권한을 할당하는 role과 rolebinding을 만드는 방법
Role: developer

Role Resources: pods

Role Actions: list

Role Actions: create

Role Actions: delete

RoleBinding: dev-user-binding

RoleBinding: Bound to dev-user
<br></br>
role을 생성하기 위해서는
kubectl create role {role_name} --verb={actions} --verb={actions} --verb={actions} --resource={Resources} -n={namespace} 작업을 거친다.
<br></br>
user를 위한 rolebinding을 생성하기 위해서는
<br></br>
kubectl create {rolebinding_name} --role={role name to Bound} --user={user name to Bound} -n={namespace_name} 작업을 거친다.
<br></br>
ServiceAccount를 위한 rolebinding을 생성하기 위해서는
<br></br>
kubectl create {rolebinding_name} --role={role name to Bound} --serviceaccount={serviceaccount's namespace}:{serviceaccount name to Bound} -n={namespace_name} 작업을 거친다    

<br></br>
![default](./image/1126-1.PNG)
<br></br>

2. 현재 동작중인 developer role을 기반으로 dev-user에게 blue namespace에서 deployment를 만들 수 있는 권한을 할당하는 rule을 만드는 방법
(1) role을 수정한다.
role은 클러스터 전체의 namespace에서 영향력을 발휘하는 ClusterRole과는 달리 각 namespace별로 동작하기 때문에 namespace 명시를 해주어야 한다.
<br></br>
![default](./image/1126-2.PNG)
<br></br>
resource에 deployment를 추가한다. 또한 deployment를 api group이 apps인 곳에서 동작하기 때문에 apiGroups에 내용을 추가한다.

## 4. Cluster Roles
1. 다음과 같은 스펙의 Resource를 생성

 ClusterRole: storage-admin

Resource: persistentvolumes

Resource: storageclasses

ClusterRoleBinding: michelle-storage-admin

ClusterRoleBinding Subject: michelle

ClusterRoleBinding Role: storage-admin

clusterRole은 namespace에 상관없이 영향력을 발휘한다는 점을 인지한다.
Role 그리고 RoleBinding을 만드는 방법과 크게 다르지 않다.
ClusterRole의 경우는
<br></br>
kubectl create clusterrole <clusterrole_name> --resource={resource to permit} --verb={action to permit}
--resource와 --verb는 ,를 붙이지 않고 여러 개 쓸 수 있다.
ClusterRoleBinding의 경우는
<br></br>
kubectl create clusterrolebinding <clusterrolebinding_name> --clusterrole={clusterRole to Bound} --user={user to permit}
의 형태로 작성한다. subject의 경우 user도 될 수 있고 serviceaccount도 될 수 있는데, user가 아닌 serviceaccount의 경우
--serviceaccount={'namespace name' in serviceaccount}:{serviceaccount name}의 형태로 작성한다.
```
controlplane ~ ➜ kubectl create clusterrole storage-admin --resource=persistentvolume --resource=storageclasses --verb=get --verb=list --verb=watch --verb=create --verb=delete
clusterrole.rbac.authorization.k8s.io/storage-admin created
controlplane ~ ➜ kubectl create clusterrolebinding --clusterrole=storage-admin --user=michelle
clusterrolebinding.rbac.authorization.k8s.io/michelle-storage-admin created
```
## 5. Service Account
1. default namespace에 dashboard-sa라는 Service Account가 있다. Pod에 대해 list를 허용하는 Role과 RoleBinding을 생성하는 방법
<br></br>
(1). Pod에 대해 list를 허용하는 Role을 생성한다.
```
controlplane ~ ➜ kubectl create role prob11role --resource=pod --verb=list
```
(2). serviceaccount인 dashboard-sa가 해당 role을 가지도록 rolebinding을 생성한다.
```
controlplane ~ ➜ kubectl create rolebinding prob11rolebinding --role=prob11role --serviceaccount=default:dashboard-sa
```
현재 default namespace에서 dashboard 파드가 실행되고 있다.
<br></br>
![default](./image/1126-3.PNG)
<br></br>
default serviceaccount를 사용하고 있기 때문에 forbidden 되어 있다. dashboard-sa라는 serviceaccount는 해당 리소스에 대한 행동 권한을 얻었으므로, dashboard-sa로 인증하기 위해 dashboard-sa에 대한 token을 생성한다.
kubectl create token {serviceaccount_name}
```
controlplane ~ ➜ kubectl create token dashboard-sa
eyJhbGciOiJSUzI1NiIsImtpZCI6IlVEVVFWV3JyYTJKUHZLX0xPSGF2U00zckp3QWJZQjZSMkFRSVdBdUE4MVkifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiLCJrM3MiXSwiZXhwIjoxNjY5NDU0Mjg5LCJpYXQiOjE2Njk0NTA2ODksImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJkZWZhdWx0Iiwic2VydmljZWFjY291bnQiOnsibmFtZSI6ImRhc2hib2FyZC1zYSIsInVpZCI6IjYxZjQyNWJiLWI1NGMtNGRkYy1iMDI4LTI2NzU4ZTJkYmEwYSJ9fSwibmJmIjoxNjY5NDUwNjg5LCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6ZGVmYXVsdDpkYXNoYm9hcmQtc2EifQ.jlLLiZLZaoI1Kq76DZN8S2Kpws-2uMMhKRlMCXAe0hCr2PcTvIM8iluLryzdU_r3m9LhSP1vzPiWK-h6_6TbkVKX9FhCWAXb8dD-Vl0XWbrb1IuVDzujkRF9WjbdtrqaDq4Mlc6mLHdaP83sfTf3AihL5K6hcwb_eZyUbgUQlzHnZ3YyIqwBSxboChVVNyiZNt1YaWvJXi4IBnGgcO5XnYkJnNwrfdOb5obd3Er-8bQN3fpZbKbTvGMGrzm-qaIHbweOOMy-ztXP8d9090w83ABEvclrPFo3o3NXcNEXa5udwbxeQhsUtpmZ9ItE-B13_Mh6v2q8T7ftlSHAbGLk0A
```
해당 토큰을 복사하여 넣어보면,
<br></br>
![default](./image/1126-4.PNG)
<br></br>
문제없이 잘 실행되는 것을 확인할 수 있다.

2. 현재 위에서 실행한 dashboard서버가 web-dashboard라는 deployment에 있는데, 해당 deployment가 serviceaccount가 default로 설정되어 있어 다른 serviceaccount가 접근을 시도할 때마다 해당 serviceaccount의 토큰을 생성하고, 적용하여야 한다.
따라서 복잡한 과정를 따르기 보다는 deployment의 기본 serviceaccount를 dashboard-sa로 바꾸는 방법
<br></br>
kubernetes.io/docs에 serviceaccount 키워드로 검색하면, Pod에서 어떻게 명시해야 되는지에 대한 예시가 나온다. 비단 service account뿐만이 아닌 PV,Secret,Configmap,PVC등 kubernetes 리소스에 대한 내용을 키워드로 검색하면 Pod에서 어떻게 쓰이는지에 대한 예시가 나온다.
https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/
<br></br>
![default](./image/1126-6.PNG)
<br></br>
Pod의 spec 바로 밑에 serviceAccountName이라는 내용으로 해당 값을 명시해야 된다는 것을 알 수 있다.
Deployment의 경우에는 spec밑의 template밑의 spec밑에 명시해야 된다는 점을 주의한다.
이제 kubectl edit을 통해 serviceaccount내용을 추가한다.
<br></br>
![default](./image/1126-5.PNG)
<br></br>
<br></br>
![default](./image/1126-7.PNG)
<br></br>
default serviceAccountName을 pod에 대한 list 권한이 있는 role을 rolebinding받은 serviceAccount인 dashboard-sa로 바꾸었다. 그 결과 token을 따로 입력하지 않아도 list가 잘 되는 것을 확인할 수 있다.

##6 NetworkPolicy
1. 다음과 같은 networkpolicy가 있다.
```
controlplane ~ ➜ kubectl get networkpolicy
NAME             POD-SELECTOR   AGE
payroll-policy   name=payroll   37s
```
networkpolicy가 적용되고 있는 Pod가 어떤 Pod인지 확인하는 방법
<br></br>
POD-SELECTOR를 기준으로 Pod를 Get한다.
kubectl get po --selector={key}={name} 의 command를 이용한다.
```
controlplane ~ ➜ kubectl get pod --selector=name=payroll
NAME      READY   STATUS    RESTARTS   AGE
payroll   1/1     Running   0          96s
```
2. networkpolicy에 대한 내용을 살펴본다.
<br></br>
![default](./image/1126-9.PNG)
<br></br> 
Label이 name=payroll인 Pod에 대해서 해당 policy를 적용한다.
<br></br>
PodSelector가 name=internal인 Pod로부터 온 traffic에 대하여, podSelector가 name=payroll인 Pod에 대해서만 ingress traffic을 허용하며, 8080Port로 보낸다는 것을 확인할 수 있다.
<br></br>
PodSelector가 name=payroll인 Pod에 대해서 ping test를 진행한다.
Host Name에는 해당 Pod의 Ip를 넣어주고 Port에는 8080을 넣어준다.
Internal-service는 Selector를 name=internal을 가지고 있고, external-service는 Selector를 name=external을 가지고 있다.
internal Pod는 name=internal로 Labeling 되어 있고, external Pod는 name=external로 Labeling되어 있다.
<br></br>
![default](./image/1126-10.PNG)
<br></br> 
<br></br>
![default](./image/1126-11.PNG)
<br></br>
internal application은 payroll service에 접근할 수 있지만, external applicationd은 timeout되는 것을 확인할 수 있다. 
<br></br>
![default](./image/1126-12.PNG)
<br></br>
다음과 같은 아키텍처를 가질때, internal Pod에서 external Pod에 영향을 미치고 있는 external-service에 대한 접근이 가능한지 확인한다. external-service의 clusterIP에 접근이 가능한지를 확인하면 된다.
<br></br>
![default](./image/1126-13.PNG)
<br></br>
<br></br>
![default](./image/1126-14.PNG)
<br></br>
internal->external이 문제없이 잘 동작하는 모습이다.
3. Internal Pod에 대하여 payroll-service와 db-service에서 오는 traffic만을 허용하는 networkpolicy를 생성하는 방법
Policy Name: internal-policy

Policy Type: Egress

Egress Allow: payroll

Payroll Port: 8080

Egress Allow: mysql

MySQL Port: 3306

kubernetes.io/docs에 networkpolicy를 키워드로 검색한다.
https://kubernetes.io/docs/concepts/services-networking/network-policies/
<br></br>
![default](./image/1126-15.PNG)
<br></br>
docs의 내용과 문제에 제시된 내용을 기반으로 Egress에 대한 내용을 작성한다. Ingress에 대한 제약은 없으므로 Ingress는 {}로 명시한다.
<br></br>
![default](./image/1126-16.PNG)
<br></br>
PodSelector를 명시해야 하기 때문에 networkpolicy를 적용할 internal Pod의 label이 name=internal인 것을 확인한다.
<br></br>
ingress 혹은 egress를 여러 대상에 대해 적용해야 될 떄는 from 혹은 to를 추가로 작성하여 해당 내용을 작성하면 된다.
<br></br>
![default](./image/1126-17.PNG)
<br></br>
