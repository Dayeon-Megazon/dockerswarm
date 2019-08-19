# docker on AWS

*Let's make docker on AWS*

---
## make instance on EC2

|         Setting          |  Value                |
|------------------- | -------------------|
| AMI  | Amazon Linux AMI 2018.03.0 (HVM), SSD Volume Type |
| Type                | t2.medium                 |

## Security Group

| Protocol  | Port                |Role|
|------------------- | -------------------|-------------------|
| TCP  | 6379 | Redis|
| TCP  | 3306 | MariaDB|
| TCP  | 9092 | Kafka|
| TCP  | 2181 | Zookeeper|

## Create EIP and Attach EIP to EC2 instance

  [Create AWS EIP](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html)
  
## SSH to Created EC2 instance

> Tip: Services -> EC2 Dashboard -> Right Click Instance -> Click "Connections"

```
$sudo ssh -i "key-pair-path" ec2-user@<instance-address>
```

```
$ssh -i "dayeon_docker.pem" ec2-user@ec2-54-180-188-231.ap-northeast-2.compute.amazonaws.com



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