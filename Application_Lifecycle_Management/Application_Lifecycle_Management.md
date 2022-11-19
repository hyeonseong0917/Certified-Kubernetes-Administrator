# Kubernetes Application Lifecycle Management
## 1. Rolling Updates and Rollbacks
1. 현재 frontend라는 deployment가 kodekloud/webapp-color:v1의 Image를 가지고 Rolling Update로 동작하고 있다. 해당 deployment의 Image를 kodekloud/webapp-color:v2로 upgrade하는 방법
<br></br>
kubernetes.io/docs에 cheat sheet 키워드로 검색한다.
https://kubernetes.io/ko/docs/reference/kubectl/cheatsheet/
<br></br>
리소스 업데이트 부분을 살펴보면,
![default](./image/1119-1.PNG)
<br></br>
kubectl set image deployment {deployment_name} {container_name}={new_image}:{new_version} 커맨드를 사용해 업데이트 한다. 중간의 /는 없어도 무방하다.
<br></br>
업데이트 하기 전에 기존 deploy를 describe해서 containter_name을 얻어야 한다.
<br></br>
![default](./image/1119-2.PNG)
<br></br>
simple-webapp 이라는 container_name을 가진다.
최종적으로 다음 명령을 통해 Update한다.
```
$ kubectl set image deploy frontend simple-webapp=kodekloud/webapp-color:v2
```

## 2. Commands and Arguments