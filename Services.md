# Basic Usage of Docker Swarm

*Let's make servies*

---
## Configuring Services

> start your master1 instance

```
$ docker service create --name nginx_service --replicas 3 nginx

i2dlt5gw561zga0k8hxch5yhu
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
