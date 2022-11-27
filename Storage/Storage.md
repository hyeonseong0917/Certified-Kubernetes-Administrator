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

3. 다음 spec을 가지는 Persistent Volume 생성한다.
<br></br>
Volume Name: pv-log
<br></br>
Storage: 100Mi
<br></br>
Access Modes: ReadWriteMany
<br></br>
Host Path: /pv/log
<br></br>
Reclaim Policy: Retain
<br></br>
kubernetes.io/docs에 Persistent Volume 키워드로 검색한다.
https://kubernetes.io/docs/concepts/storage/persistent-volumes/
<br></br>
![default](./image/1127-3.PNG)
<br></br>
이 경우는 nfs대신 hostPath를 넣어 작성한다.
<br></br>
![default](./image/1127-4.PNG)
<br></br>
4. Persistent Volume Claim를 해당 spec에 맞게 생성한다.
Volume Name: claim-log-1
<br></br>
Storage Request: 50Mi
<br></br>
Access Modes: ReadWriteOnce
<br></br>
PV docs에 같이 존재한다.
<br></br>
![default](./image/1127-5.PNG)
<br></br>
마찬가지로 해당하는 내용들을 기입한다.
<br></br>
![default](./image/1127-6.PNG)
<br></br>