# 3# Service 만들어서..

안녕하세요.

챕터 #2에서 배운 Docker-Swarm이 잘 생성되었나요?

이번 챕터는 서비스를 만든 후에, Docker-Swarm으로 잘 분산이 되는지 확인해보도록 합시다.

---

### 목차는 다음과 같습니다.

[1. 서비스 만들기](#1-서비스-만들기)  
[2. 나머지 Node들을 Woker로 만들기](#2-나머지-node들을-woker로-만들기)   
  
---

## 1. 서비스 만들기

manager로 만들었던 aws-node1에서 진행하도록 합니다.   
이 노드에서 서비스를 만들어봅시다.

`사용법`
```
$ docker service create [OPTIONS] IMAGE [COMMAND] [ARG...]
```
`예시`     
```
ubuntu@aws-node1:~$ sudo docker service create --name web httpd

i7pbste6l5f01s1ps0ie3mlbz
overall progress: 1 out of 1 tasks 
1/1: running   [==================================================>] 
verify: Service converged 
```
서비스가 잘 생성되었는지 확인해보겠습니다.
```
ubuntu@aws-node1:~$ sudo docker service ls

ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
i7pbste6l5f0        web                 replicated          1/1                 httpd:latest        
````
생성한 서비스 web의 상태정보도 확인해보겠습니다.
```
ubuntu@aws-node1:~$ sudo docker service ps web
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE           ERROR       
pfxfmfhouu7h        web.1               httpd:latest        aws-node1           Running             Running 3 minutes ago               
```

## 2. 서비스 복제하기

만든 서비스를 복제해봅시다.  
복제하는데는 여러 방법이 있습니다.  

- = 기호를 사용하여 복제하기  

`사용법`
```
$ sudo docker service scale [service-name]=[replica-number]
```
`예시`
```
ubuntu@aws-node1:~$ sudo docker service scale web=3

web scaled to 3
overall progress: 3 out of 3 tasks 
1/3: running   [==================================================>] 
2/3: running   [==================================================>] 
3/3: running   [==================================================>] 
verify: Service converged 
```
`복제 확인`
```
ubuntu@aws-node1:~$ sudo docker service ls

ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
i7pbste6l5f0        web                 replicated          3/3                 httpd:latest        
```
```
ubuntu@aws-node1:~$ sudo docker service ps web

ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR       
pfxfmfhouu7h        web.1               httpd:latest        aws-node1           Running             Running 17 minutes ago
ankki0tclob3        web.2               httpd:latest        aws-node3           Running             Running 28 seconds ago
mkdc14bvys8l        web.3               httpd:latest        aws-node2           Running             Running 28 seconds ago               
```
