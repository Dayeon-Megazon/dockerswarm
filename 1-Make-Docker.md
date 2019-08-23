# AWS에서 EC2 인스턴스로 Docker-Swarm 만들기

안녕하세요.

AWS에서 EC2 인스턴스를 사용해서 Docker-Swarm을 만드는 방법을 배워보겠습니다.

우선 사용 전에, OS가 `Windows`인 경우에는 가상머신을 사용하는 방법을 권장드립니다.

> 가상머신 설치하기 :  [VMware install](https://m.blog.naver.com/PostView.nhn?blogId=bellship99&logNo=221164040584&proxyReferer=https%3A%2F%2Fwww.google.com%2F) 

---

### 목차는 다음과 같습니다.

[1. EC2 인스턴스 만들기](#1-ec2-인스턴스-만들기)  
[2. SSH를 통해 EC2 인스턴스에 접속하기](#2-ssh를-통해-ec2-인스턴스에-접속하기)   
[3. 접속한 인스턴스 안에 Docker를 설치하기](#3-접속한-인스턴스-안에-docker를-설치하기)  
[4. Docker-compose 설치하기](#4-docker-compose-설치하기)


---
## 1. EC2 인스턴스 만들기

AWS 사이트에 들어가서 새로운 EC2 인스턴스를 하나 만들어 봅시다.

`서울 리전`에서 [인스턴스 시작] 버튼을 눌러 인스턴스를 만들겠습니다.

| 구성 | <center>값</center>|
| :---: |--- |
| AMI | Amazon Linux AMI 2018.03.0 (HVM) |
| 인스턴스 유형 | t2.medium |
| 인스턴스 구성 | default |
| 스토리지 추가 | default |
| 태그 추가 | 키: Name, 값: AWS-Docker |     
| 보안 그룹 구성 | 새 보안 그룹 생성 후,  모든 트래픽 개방 |

> 키 페어는 기존에 쓰시던 거를 사용해도 되며, 새로운 키 페어를 생성하신 경우엔 파일을 잘 보관해주시길 바랍니다.  
> 가상머신을 사용하여 작업할 경우에는 가상머신 안에 키 페어 파일을 복사해서 넣어두셔야 합니다.

## 2. SSH를 통해 EC2 인스턴스에 접속하기

인스턴스가 생성이 되었으면, `연결' 버튼을 누릅니다.

인스턴스에 엑세스하는 방법을 그대로 따라합니다.

`연결 방법`

```
$ chmod 400 [key-pair-path]
$ sudo ssh -i "key-pair-path" ec2-user@<instance-address>
```

`예시`

```
$ chmod 400 dayeon_docker.pem
$ ssh -i "dayeon_docker.pem" ec2-user@ec2-54-180-188-231.ap-northeast-2.compute.amazonaws.com



       __|  __|_  )
       _|  (     /   Amazon Linux AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-ami/2018.03-release-notes/
8 package(s) needed for security, out of 11 available
Run "sudo yum update" to apply all updates.
```
  
## 3. 접속한 인스턴스 안에 Docker를 설치하기 

위의 예시에서 연결한 후에 나온 문구인 `Run "sudo yum update" to apply all updates.` 처럼  
apply 모두를 업데이트 한 후에 docker를 설치해보도록 하겠습니다.

```
[ec2-user@ip-172-31-18-132 ~]$ $ sudo yum -y upgrade
[ec2-user@ip-172-31-18-132 ~]$ $ sudo yum -y install docker
```

docker가 제대로 설치되었는지 확인해보겠습니다.

```
[ec2-user@ip-172-31-18-132 ~]$ docker -v
Docker version 18.09.1, build 4c52b90
```

설치되었으므로 이제 도커를 시작해보겠습니다.

```
[ec2-user@ip-172-31-18-132 ~]$ sudo service docker start
```

`usermod` 명령어를 사용하여 그룹에 사용자인 `ec2-user`를 추가합니다.

```
[ec2-user@ip-172-31-18-132 ~]$ sudo usermod -aG docker ec2-user
```

## 4. Docker-compose 설치하기

```
[ec2-user@ip-172-31-18-132 ~]$ sudo curl -L https://github.com/docker/compose/releases/download/1.25.0\
-rc2/docker-compose-`uname -s`-`uname -m` -o \
/usr/local/bin/docker-compose
```

설치 후에 `chmod` 명령어를 사용하여 디렉토리에 excute 권한을 추가합니다.

```
[ec2-user@ip-172-31-18-132 ~]$ sudo chmod +x /usr/local/bin/docker-compose
```

설치가 되었는지 확인해보겠습니다.

```
[ec2-user@ip-172-31-18-132 ~]$ docker-compose -v
docker-compose version 1.25.0-rc2, build 661ac20e
```



## Initialization
```
$ sudo docker swarm init --advertise-addr [your-master1-ip]
```
*example*
```
master1:~$ sudo docker swarm init --advertise-addr 54.180.188.231
Swarm initialized: current node (hwmdgy2a2vu3aoa98ehi1hu4u) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3dlz3z61zp569hqespolbigfnn5yb8j6wluo268glfnxmbl9su-4lktxp5fl5gj501jd80mvopnz 54.180.188.231:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```
 > Tip : only master1 must do this step. 
 
## Check Swarm Node

```
$ docker info
.
.
Swarm: active
 NodeID: hwmdgy2a2vu3aoa98ehi1hu4u
 Is Manager: true
 ClusterID: dr9b1o618ntskmlz2f9vaph9d
 Managers: 1
 Nodes: 1
 Orchestration:
  Task History Retention Limit: 5
.
.
```

## Add a worker to this swarm

```
$ docker swarm join-token worker

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-1e3rc51slo80smjkgukakdfuq7voxohs037y2cm54jnny9fltv-93mn4kp4eaxu44sfaq40shp5u 13.125.178.3:2377
```

## Join with another worker node

Join another node with the token above.
> tip : 2377 port must be open.

```
$ docker swarm join -- token [your-token-value] [your-manager1-ip]:[port-number 2377]
```
```
worker1:~$ docker swarm join --token SWMTKN-1-1e3rc51slo80smjkgukakdfuq7voxohs037y2cm54jnny9fltv-93mn4kp4eaxu44sfaq40shp5u 13.125.178.3:2377
This node joined a swarm as a worker.

worker2:~$ docker swarm join --token SWMTKN-1-1e3rc51slo80smjkgukakdfuq7voxohs037y2cm54jnny9fltv-93mn4kp4eaxu44sfaq40shp5u 13.125.178.3:2377
This node joined a swarm as a worker.
```

## Check docker node

```
master1:~$ docker node ls

ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
q6f7b4bghbmwjmdud2p4bly7v *   ip-172-31-20-162    Ready               Active              Leader              18.06.1-ce
3w208wado67n2ftoyoddddn97     ip-172-31-23-216    Ready               Active                                  18.06.1-ce
k9iihxr5m2o126lsuqyh99iuu     ip-172-31-29-251    Ready               Active                                  18.06.1-ce
```
