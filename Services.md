# Basic Usage of Docker Swarm

*Let's make servies*

---
## Configuring Services

> start your master1 instance
```
$ docker service create [OPTIONS] IMAGE [COMMAND] [ARG...]
```
```
$ docker service create --name nginx_service --replicas 3 nginx

i2dlt5gw561zga0k8hxch5yhu
overall progress: 3 out of 3 tasks 
1/3: running   [==================================================>] 
2/3: running   [==================================================>] 
3/3: running   [==================================================>] 
verify: Service converged 
```

## Check Docker Service
```
$ docker service ls

ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
i2dlt5gw561z        nginx_service       replicated          0/3                 nginx:latest    
.
.
.
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
i2dlt5gw561z        nginx_service       replicated          3/3                 nginx:latest    
```
> Tip : check more detail

```
$ docker service ps [SERVICE]
```
```
$ docker service ps nginx_service
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE        ERROR ...      
dyg4u56g31q0        nginx_service.1     nginx:latest        ip-172-31-29-251    Running             Running 9 minutes ago
kb5acm6p9j84        nginx_service.2     nginx:latest        ip-172-31-20-162    Running             Running 9 minutes ago
n8nzhh2fm3zd        nginx_service.3     nginx:latest        ip-172-31-23-216    Running             Running 9 minutes ago     
```

## Docker Swarm Service Scale-out
```
$ docker service scale SERVICE=REPLICAS [SERVICE=REPLICAS...]
```
```
$ docker service scale nginx_service=3

nginx_service scaled to 3
overall progress: 3 out of 3 tasks 
1/3: running   [==================================================>] 
2/3: running   [==================================================>] 
3/3: running   [==================================================>] 
verify: Service converged 
```
```
$ docker service ls

ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
i2dlt5gw561z        nginx_service       replicated          3/3                 nginx:latest        
```
```
$ for i in $(cat /etc/hosts | grep manager| awk '{print $1}')
> do
> ssh ec2-user@ip-172-31-20-162 "docker ps -a"
> done
```
```
$ docker service ps nginx_service
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE       ERROR ...      
dyg4u56g31q0        nginx_service.1     nginx:latest        ip-172-31-29-251    Running             Running 17 minutes ago
kb5acm6p9j84        nginx_service.2     nginx:latest        ip-172-31-20-162    Running             Running 17 minutes ago 
n8nzhh2fm3zd        nginx_service.3     nginx:latest        ip-172-31-23-216    Running             Running 17 minutes ago

```
