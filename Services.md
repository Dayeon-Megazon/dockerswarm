# Basic Usage of Docker Swarm

*Let's make servies*

---
## Configuring Services

> start your master1 instance
```
$ docker service create [OPTIONS] IMAGE [COMMAND] [ARG...]
```
> 
```
master1:~$ docker service create --name web httpd

xlqf7myjqi688ik5xnla1c2oz
overall progress: 1 out of 1 tasks 
1/1: running   [==================================================>] 
verify: Service converged 
```

## Check Docker Service
```
$ docker service ls

ID                  NAME                MODE                REPLICAS            IMAGE               PORTS    
xlqf7myjqi68        web                 replicated          0/1                 httpd:latest        
.
.
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS    
xlqf7myjqi68        web                 replicated          1/1                 httpd:latest        
```

> Tip : check more detail
```
$ docker service ps [SERVICE]
```
```
$ docker service ps web

ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR...
wx6jrekulqwt        web.1               httpd:latest        ip-172-31-20-162    Running             Running 29 seconds ago              
```

## Docker Swarm Service Scale-out
```
$ docker service scale SERVICE=REPLICAS [SERVICE=REPLICAS...]
```
```
$ docker service scale web=3

web scaled to 3
overall progress: 3 out of 3 tasks 
1/3: running   [==================================================>] 
2/3: running   [==================================================>] 
3/3: running   [==================================================>] 
verify: Service converged 
```
```
$ docker service ls

ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
xlqf7myjqi68        web                 replicated          3/3                 httpd:latest 
```
```
$ docker service ps web

ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE             ERROR...
wx6jrekulqwt        web.1               httpd:latest        ip-172-31-20-162    Running             Running about a minute ago
vf7m5p3of4kl        web.2               httpd:latest        ip-172-31-29-251    Running             Running 53 seconds ago
oix9zy3o8gtd        web.3               httpd:latest        ip-172-31-23-216    Running             Running 53 seconds ago 
```

## Shut down the Docker Swarm Service
```
$ docker service rm SERVICE [SERVICE...]
```
```
$ docker service rm web

web
```
```
$ docker service ls

ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
```
```
$ docker service ps web

no such service: web
```
## Uninstall Docker Swarm
```
$ docker swarm leave -f

Node left the swarm.
```
