# service maintenance

## Rolling Update
```
master1~:$ docker service create --name ping --replicas 4 --update-delay 10s alpine ping docker.com

ezykopvmtg9bvb5e51n5i64kr
overall progress: 4 out of 4 tasks 
1/4: running   [==================================================>] 
2/4: running   [==================================================>] 
3/4: running   [==================================================>] 
4/4: running   [==================================================>] 
verify: Service converged 
```

## Check to Service

```
$ docker service inspect --pretty ping

ID:		ezykopvmtg9bvb5e51n5i64kr
Name:		ping
Service Mode:	Replicated
 Replicas:	4
Placement:
UpdateConfig:
 Parallelism:	1
 Delay:		10s
 On failure:	pause
 Monitoring Period: 5s
 Max failure ratio: 0
 Update order:      stop-first
RollbackConfig:
 Parallelism:	1
 On failure:	pause
 Monitoring Period: 5s
 Max failure ratio: 0
 Rollback order:    stop-first
ContainerSpec:
 Image:		alpine:latest@sha256:6a92cd1fcdc8d8cdec60f33dda4db2cb1fcdcacf3410a8e05b3741f44a9b5998
 Args:		ping docker.com 
 Init:		false
Resources:
Endpoint Mode:	vip
```
## Start to Update

- check to service task
```
$ docker service ps ping

ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE           ERROR               PORTS
fpwwl0ydkzko        ping.1              alpine:latest       ip-172-31-20-162    Running             Running 8 minutes ago                       
murpranl6kv7        ping.2              alpine:latest       ip-172-31-29-251    Running             Running 8 minutes ago                       
2syy4u1str2t        ping.3              alpine:latest       ip-172-31-29-251    Running             Running 8 minutes ago                       
nkgxiqc9zd06        ping.4              alpine:latest       ip-172-31-23-216    Running             Running 8 minutes ago          
```

 - updeate 
 ```
 $ docker service update --image alpine:3.7 ping

ping
overall progress: 4 out of 4 tasks 
1/4: running   [==================================================>] 
2/4: running   [==================================================>] 
3/4: running   [==================================================>] 
4/4: running   [==================================================>] 
verify: Service converged 
 ```
 ---
 ## Rollback
