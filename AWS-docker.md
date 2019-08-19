# Make docker on AWS

*Let's make docker on AWS*

---
## make instance on EC2

| Name                  | AMI       | Instance Type     | Firewall |
|-------------------    | -------------------| -------------------| -------------------|
| master1  | Amazon Linux AMI 2018.03.0 (HVM), SSD Volume Type | t2.medium  | Allow 2377 Port |
| worker1  | Amazon Linux AMI 2018.03.0 (HVM), SSD Volume Type | t2.medium  | Allow 2377 Port |
| worker2  | Amazon Linux AMI 2018.03.0 (HVM), SSD Volume Type | t2.medium  | Allow 2377 Port |

## Create EIP and Attach EIP to EC2 instance

  [Create AWS EIP](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html)
  
## SSH to Created EC2 instance

> Tip: Services -> EC2 Dashboard -> Right Click Instance -> Click "Connections"

```
$ sudo ssh -i "key-pair-path" ec2-user@<instance-address>
```

```
$ ssh -i "dayeon_docker.pem" ec2-user@ec2-54-180-188-231.ap-northeast-2.compute.amazonaws.com



       __|  __|_  )
       _|  (     /   Amazon Linux AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-ami/2018.03-release-notes/
8 package(s) needed for security, out of 11 available
Run "sudo yum update" to apply all updates.
```

## Install docker

```
$ sudo yum -y upgrade
$ sudo yum -y install docker
```
```
$ docker -v

Docker version 18.09.1, build 4c52b90
```

```
$ sudo service docker start
$ sudo usermod -aG docker ec2-user
```
## Install docker-compose

```
$ sudo curl -L https://github.com/docker/compose/releases/download/1.25.0-rc2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```
```
$ docker-compose -v

docker-compose version 1.25.0-rc2, build 661ac20e
```
> Tip: Docker and docker-compose must be installed on master1, worker1, worker2.

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
