# Kubernetes Logging and Monitoring
## 1. Monitor-Cluster-Components
1. 가장 많은 CPU를 소비하는 node를 찾는 방법

(1) 직접 확인
```
$ kubectl top node
NAME           CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
controlplane   320m         0%     1200Mi          0%        
node01         22m          0%     268Mi           0%
```
controlplane이 320m으로 가장 많은 CPU를 소비하는 것을 확인할 수 있다.
<br></br>
(2) --sort-by 옵션 + --no-headers | head -1 사용
<br></br>
--sort-by 옵션을 이용하면 내림차순으로 정렬이 되며, 
<br></br>
--no-headers는 Name,CPU를 포함하는 row를 출력하지 않는다는 것이며, 이 출력에 대해 head -1은 1개만을 뽑는 것이기 때문에
가장 값이 큰 항목 1개만 출력이 된다.
```
$ kubectl top node --sort-by='cpu' --no-headers | head -1
controlplane   312m   0%    1202Mi   0%
```

2. 가장 적은 CPU를 소비하는 pod를 찾는 방법

동일하게 top pod 명령에 --sort-by 옵션을 붙이지만, head가 아닌 밑에서부터 추출해야 하기 때문에 tail을 사용한다.
```
$ kubectl top pod --sort-by='cpu' --no-headers | tail -1
lion       1m     18Mi
```

## 2. Managing Application Logs