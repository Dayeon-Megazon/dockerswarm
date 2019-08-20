# Service Maintenance

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

 - update 
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
  - check to task log
  ```
  $ docker service ps ping
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR       
w4ulvln1z4bc        ping.1              alpine:3.7          ip-172-31-20-162    Running             Running 2 minutes ago
fpwwl0ydkzko         \_ ping.1          alpine:latest       ip-172-31-20-162    Shutdown            Shutdown 2 minutes ago
rid9m34lpl0i        ping.2              alpine:3.7          ip-172-31-29-251    Running             Running 2 minutes ago 
murpranl6kv7         \_ ping.2          alpine:latest       ip-172-31-29-251    Shutdown            Shutdown 2 minutes ago
nm0r0kqqagry        ping.3              alpine:3.7          ip-172-31-29-251    Running             Running 3 minutes ago 
2syy4u1str2t         \_ ping.3          alpine:latest       ip-172-31-29-251    Shutdown            Shutdown 3 minutes ago
rxlrcukmqxlp        ping.4              alpine:3.7          ip-172-31-23-216    Running             Running 2 minutes ago 
nkgxiqc9zd06         \_ ping.4          alpine:latest       ip-172-31-23-216    Shutdown            Shutdown 2 minutes ago               
  ```
 ---
 ## Rollback
 ```
 $ docker service rollback ping

ping
rollback: manually requested rollback 
overall progress: rolling back update: 4 out of 4 tasks 
1/4: running   [>                                                  ] 
2/4: running   [>                                                  ] 
3/4: running   [>                                                  ] 
4/4: running   [>                                                  ] 
verify: Service converged 
 ```
 
 ## Check to task log
 ```
 $ docker service ps ping
 
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE                 ERROR 
j3efukwmufo4        ping.1              alpine:latest       ip-172-31-20-162    Running             Running 57 seconds ago
w4ulvln1z4bc         \_ ping.1          alpine:3.7          ip-172-31-20-162    Shutdown            Shutdown 57 seconds ago
fpwwl0ydkzko         \_ ping.1          alpine:latest       ip-172-31-20-162    Shutdown            Shutdown 6 minutes ago
olom1azb6f4c        ping.2              alpine:latest       ip-172-31-29-251    Running             Running 45 seconds ago
rid9m34lpl0i         \_ ping.2          alpine:3.7          ip-172-31-29-251    Shutdown            Shutdown 46 seconds ago
murpranl6kv7         \_ ping.2          alpine:latest       ip-172-31-29-251    Shutdown            Shutdown 5 minutes ago 
imj9xz1rrdzw        ping.3              alpine:latest       ip-172-31-29-251    Running             Running about a minute ago
nm0r0kqqagry         \_ ping.3          alpine:3.7          ip-172-31-29-251    Shutdown            Shutdown about a minute ago
2syy4u1str2t         \_ ping.3          alpine:latest       ip-172-31-29-251    Shutdown            Shutdown 6 minutes ago
42its5m9f37k        ping.4              alpine:latest       ip-172-31-23-216    Running             Running 33 seconds ago
rxlrcukmqxlp         \_ ping.4          alpine:3.7          ip-172-31-23-216    Shutdown            Shutdown 34 seconds ago
nkgxiqc9zd06         \_ ping.4          alpine:latest       ip-172-31-23-216    Shutdown            Shutdown 5 minutes ago     
 ```
 
