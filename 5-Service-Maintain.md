## 7. Node를 유지보수 하기 위해 Drain 상태로 전환하기

Node를 유지보수하기 위해서, Node에서 실행되는 작업을 빼내야할 때 `Drain` 설정을 사용합니다.

저는 위에서 만든 Ping 서비스의 Task 내역을 다 지운 후 다시 서비스를 생성하는 작업을 하겠습니다.
여러분께서는 이 작업을 안하셔도 괜찮습니다.
 
 > 참고 : 챕터 #3의 `3.서비스 지우기`와 `1. 서비스 만들기`   
 > [챕터 #3](https://github.com/It-dayeon/dockerswarm/blob/master/3-Make-Service.md)

---

다시 ping을 생성한 후의 Task 상태를 확인해보겠습니다.

```
ubuntu@aws-node1:~$ ubuntu@aws-node1:~$ sudo docker service ps ping
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR       
5ao38pqjme6t        ping.1              alpine:latest       aws-node2           Running             Running 45 seconds ago
tjtpthtxayhu        ping.2              alpine:latest       aws-node2           Running             Running 45 seconds ago
lcr9l2nqq64n        ping.3              alpine:latest       aws-node3           Running             Running 45 seconds ago
fpcd0pnbdrzb        ping.4              alpine:latest       aws-node1           Running             Running 46 seconds ago       
```
4개의 복제본이 node에 `분산분배`되어있는 것을 확인할 수 있습니다.

```
ubuntu@aws-node1:~$ sudo docker node ls

ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
6m0jz67jhevvsk9sbqklna83x *   aws-node1           Ready               Active              Leader              19.03.1
hqkfbmk7byfsboh0w6v0wjspn     aws-node2           Ready               Active                                  19.03.1
sexjhrlvhuyxjgu6w5tuwfimk     aws-node3           Ready               Active                                  19.03.1
```
node의 상태를 확인하면, node들이 모두 `Active` 되어 있고 `Ready` 중인 것을 확인할 수 있습니다.    

> 이 부분에서 오류가 난다면, EC2 인스턴스를 재부팅하거나 docker swarm을 해제하고 다시 join하시길 바랍니다.

----

ping이 node들에 잘 분산적으로 분배가 되어있는 것을 확인해보셨나요?   
그럼 이제 유지보수 모드로 진입해보겠습니다.

저는 `aws-node3`을 availability를 `Active`에서 `Drain`으로 전환해보도록 하겠습니다.

```
ubuntu@aws-node1:~$ sudo docker node update --availability drain aws-node3

aws-node3
```
위에서 배운 update 명령에 `--availablity` 옵션을 사용하여 Drain 으로 전환하였습니다.

```
ubuntu@aws-node1:~$ sudo docker node ls

ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
6m0jz67jhevvsk9sbqklna83x *   aws-node1           Ready               Active              Leader              19.03.1
hqkfbmk7byfsboh0w6v0wjspn     aws-node2           Ready               Active                                  19.03.1
sexjhrlvhuyxjgu6w5tuwfimk     aws-node3           Ready               Drain                                   19.03.1
```
다음과 같이 `aws-node3`이 Drain 상태로 변경된 것을 확인할 수 있습니다.

이제 서비스 ping의 상태를 확인해봅시다.
```
ubuntu@aws-node1:~$ sudo docker service ps ping

ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR       
5ao38pqjme6t        ping.1              alpine:latest       aws-node2           Running             Running 11 minutes ago
tjtpthtxayhu        ping.2              alpine:latest       aws-node2           Running             Running 11 minutes ago
d15frlad5zz3        ping.3              alpine:latest       aws-node1           Running             Running 3 minutes ago
lcr9l2nqq64n         \_ ping.3          alpine:latest       aws-node3           Shutdown            Shutdown 3 minutes ago
fpcd0pnbdrzb        ping.4              alpine:latest       aws-node1           Running             Running 11 minutes ago         
```
`aws-node3`에 올라와 있던 task인 `ping.3`이 `Shutdown` 상태로 전환되었습니다.   
다른 노드인 `aws-node1`이 원래 `aws-node3`이 하던 task를 수행하고 있습니다. 

Node의 유지보수 작업을 끝냈다고 가정한 후에, 다시 Active 상태로 돌아가보겠습니다.
```
ubuntu@aws-node1:~$ sudo docker node update --availability active aws-node3

aws-node3
```
`Active`로 상태를 전환한 후, 다시 task를 확인해보겠습니다.

```
ubuntu@aws-node1:~$ sudo docker service ps ping

ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR       
5ao38pqjme6t        ping.1              alpine:latest       aws-node2           Running             Running 16 minutes ago
tjtpthtxayhu        ping.2              alpine:latest       aws-node2           Running             Running 16 minutes ago
d15frlad5zz3        ping.3              alpine:latest       aws-node1           Running             Running 7 minutes ago 
lcr9l2nqq64n         \_ ping.3          alpine:latest       aws-node3           Shutdown            Shutdown 7 minutes ago
fpcd0pnbdrzb        ping.4              alpine:latest       aws-node1           Running             Running 16 minutes ago               
```
`aws-node3`을 다시 Active 상태로 전환하였지만, 기존에 했던 작업을 다시 이어받아 할 수 는 없습니다.

----
*응용하기*

그렇다면, `aws-node3`에 다시 일을 줄 수 있는 방법이 있을까요?

`aws-node3`에도 task를 다시 주고 싶다면, ping의 replicas의 수를 늘리면 가능합니다!  
서비스 ping의 수를 5까지 늘려보겠습니다. 
```
ubuntu@aws-node1:~$ sudo docker service scale ping=5

ping scaled to 5
overall progress: 5 out of 5 tasks 
1/5: running   [==================================================>] 
2/5: running   [==================================================>] 
3/5: running   [==================================================>] 
4/5: running   [==================================================>] 
5/5: running   [==================================================>] 
verify: Service converged 
```
`aws-node3`이 일을 받았는지 확인해보겠습니다.
```
ubuntu@aws-node1:~$ sudo docker service ps ping
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE                ERROR   
5ao38pqjme6t        ping.1              alpine:latest       aws-node2           Running             Running 23 minutes ago
tjtpthtxayhu        ping.2              alpine:latest       aws-node2           Running             Running 23 minutes ago
d15frlad5zz3        ping.3              alpine:latest       aws-node1           Running             Running 15 minutes ago
lcr9l2nqq64n         \_ ping.3          alpine:latest       aws-node3           Shutdown            Shutdown 15 minutes ago
fpcd0pnbdrzb        ping.4              alpine:latest       aws-node1           Running             Running 23 minutes ago
gmj46hh8xkdb        ping.5              alpine:latest       aws-node3           Running             Running about a minute ago
```
다음과 같이, `ping.5`라는 새로운 작업을 `aws-node3`이 일을 받아 `Running` 중인 것을 볼 수 있습니다. 
