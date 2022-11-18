# kubernetes Scheduling
## 1. Manual Scheduling
1. node01에 Pod를 Manually Schedule 하는법
![default](./image/1118-1.PNG)
<!-- Pod를 할당하려는 Node에 특정 label을 labeling한다. -->
kubernetes.io/docs에 manual schedule 키워드로 검색한다.
https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/
<br></br>
nodeName에 대한 설명을 보면, 다음과 같이 특정 노드에 스케줄링 할 수 있다.
<br></br>
![default](./image/1118-2.PNG)
<br></br>
## 2. Labels And Selectors
1. Pod들이 tier, env, bu로 Labeling 되어 있을 때, env Label이 dev값을 가지는 Pod의 개수를 구하는 법

(1) env label을 가지는 모든 Pod를 검색하여 값이 dev인 것을 센다.
```
$ kubectl get po -L=env
```

(2) --selector옵션을 이용해 원하는 Label:value 값만을 추출한다.
```
$ kubectl get po --selector env=dev
```
![default](./image/1118-3.PNG)

2. 중복으로 Labeling된 Pod를 검색하는 방법
--selector 뒤에 Label:value들을 콤마(,)로 구분한다
```
$ kubectl get po --selector env=prod,bu=finance,tier=frontend
```
