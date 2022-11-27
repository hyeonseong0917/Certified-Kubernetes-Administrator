# Kubernetes Storage
## 1. PersistentVolumeClaims
1. webapp이라는 Pod가 log들을 /log/app.log에 보관하고 있다. 확인 방법
kubectl exec {pod_name} -- {command} {path}
```
$ kubectl exec webapp -- cat /log/app/log
```
2. 해당 Pod의 log들을 현재 Host의 /var/log/webapp에도 저장하도록 volume을 구성하는 방법
<br></br>
Volume HostPath: /var/log/webapp
<br></br>
Volume Mount: /log를 만족시키도록 webapp Pod를 수정한다.
Volume이 HostPath이므로 kubernetes.io/docs에 hostpath를 키워드로 입력한다. https://kubernetes.io/docs/concepts/storage/volumes/
<br></br>
![default](./image/1127-1.PNG)
<br></br>
hostPath의 configuration은 다음과 같다. volumes 밑에 hostpath를 선언하고 그 위의 spec밑에 container에서 volumeMount로 그 volume을 사용하면 된다.
<br></br>
![default](./image/1127-2.PNG)
<br></br>
