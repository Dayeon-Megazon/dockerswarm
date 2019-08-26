# 3# Service 만들어서 분산이 잘 되는지 확인해보기

안녕하세요.

챕터 #2에서 배운 Docker-Swarm이 잘 생성되었나요?

이번 챕터는 서비스를 만든 후에, Docker-Swarm으로 잘 분산이 되는지 확인해보도록 합시다.

---

### 목차는 다음과 같습니다.

[1. 서비스 만들기](#1-서비스-만들기)  
[2. 서비스 복제하기](#2-서비스-복제하기)   
[3. 서비스 지우기](#3-서비스-지우기)

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

`사용법`
```
$ sudo docker service scale [service-name]=[replicas-number]
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
`복제확인`
```
ubuntu@aws-node1:~$ sudo docker service ls

ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
i7pbste6l5f0        web                 replicated          3/3                 httpd:latest        
```
`상태확인`
```
ubuntu@aws-node1:~$ sudo docker service ps web

ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR       
pfxfmfhouu7h        web.1               httpd:latest        aws-node1           Running             Running 17 minutes ago
ankki0tclob3        web.2               httpd:latest        aws-node3           Running             Running 28 seconds ago
mkdc14bvys8l        web.3               httpd:latest        aws-node2           Running             Running 28 seconds ago               
```

## 3. 서비스 지우기

위에서 만든 `web` 이라는 서비스를 지워보도록 하겠습니다.

`사용법`
```
$ docker service rm SERVICE [SERVICE...]
```
`예시`
```
ubuntu@aws-node1:~$ sudo docker service rm web
web
```
`service 목록 확인`
```
ubuntu@aws-node1:~$ sudo docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
```
`web이라는 서비스의 상태 확인`
```
ubuntu@aws-node1:~$ sudo docker service ps web

no such service: web
```

`web` 이라는 서비스를 찾을 수 없다고 나오는 것을 보아, 잘 지워진 것을 확인할 수 있습니다.

## 4. 서비스 Update 하기

서비스를 유지보수하는 Rolling update 를 테스트해보겠습니다.  
우선 새로운 서비스를 만들어봅시다.

`사용법`
```
$ sudo docker service create --name [service-name] --replicas [replicas-number] --update-delay [update-task-time] [image-name] [command]
```
`예시`
```
ubuntu@aws-node1:~$ sudo docker service create --name ping --replicas 4 --update-delay 10s alpine ping docker.com

pzooezxk56himwmece2ixchic
overall progress: 4 out of 4 tasks 
1/4: running   
2/4: running   
3/4: running   
4/4: running   
verify: Service converged 
```

* `--name` : service의 이름을 정해주는 옵션으로 여기서는 `ping`이라는 이름을 사용했습니다.
* `--replicas` : 생성할 서비스를 복제하는 옵션으로 여기서는 `4`개를 복제하겠습니다.
* `--update-delay` : 생성할 서비스에 대해 Update 진행시, 각 Task에 대해서 Update하는 동안   
                      일정한 시간 간격을 두도록 설정하는 옵션입니다. 여기서는 `10초`를 간격으로 두도록 설정했습니다.
          
* `alpine` : ping이라는 이름의 서비스를 만들 때, `alpine` 이라는 이미지를 사용합니다. 그 이후 뒤의 명령을 실행합니다.                   

> [Deploy services to a swarm](https://docs.docker.com/engine/swarm/services/#configure-a-services-update-behavior)

`서비스가 잘 생성되었는지 확인`
```
ubuntu@aws-node1:~$ sudo docker service inspect --pretty ping

ID:		pzooezxk56himwmece2ixchic
Name:		ping
Service Mode:	Replicated
 Replicas:	4
 Delay:		10s
 .
 .
 ContainerSpec:
 Image:		alpine:latest@sha256:72c42ed48c3a2db31b7dafe17d275b634664a708d901ec9fd57b1529280f01fb
 Args:		ping docker.com 
 Init:		false
```
서비스 생성 시 입력했던, `Name`, `Replicas`, `Delay` `Image` 옵션이 잘 들어간 것을 확인할 수 있습니다. 

---

[1. 서비스 만들기](#1-서비스-만들기)  
[2. 서비스 복제하기](#2-서비스-복제하기)   
[3. 서비스 지우기](#3-서비스-지우기)


