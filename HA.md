# Dockerswarm HA

----

## Worker promotion

- current state

```
$ docker node ls

ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
q6f7b4bghbmwjmdud2p4bly7v *   ip-172-31-20-162    Ready               Active              Leader              18.06.1-ce
3w208wado67n2ftoyoddddn97     ip-172-31-23-216    Ready               Active                                  18.06.1-ce
k9iihxr5m2o126lsuqyh99iuu     ip-172-31-29-251    Ready               Active                                  18.06.1-ce
```

- promotion

```
$ docker node promote ip-172-31-23-216

Node ip-172-31-23-216 promoted to a manager in the swarm.
```
## change manager status
```
$ docker node ls

ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
q6f7b4bghbmwjmdud2p4bly7v *   ip-172-31-20-162    Ready               Active              Leader              18.06.1-ce
3w208wado67n2ftoyoddddn97     ip-172-31-23-216    Ready               Active              Reachable           18.06.1-ce
k9iihxr5m2o126lsuqyh99iuu     ip-172-31-29-251    Ready               Active                                  18.06.1-ce
```

## check swarm node info leader
```
$ docker node inspect --pretty ip-172-31-20-162 

ID:			q6f7b4bghbmwjmdud2p4bly7v
Hostname:              	ip-172-31-20-162
.
.
Manager Status:
 Address:		13.125.178.3:2377
 Raft Status:		Reachable
 Leader:		Yes
```

## check swarm node info reachable
```
$ docker node inspect --pretty  ip-172-31-23-216

ID:			3w208wado67n2ftoyoddddn97
Hostname:              	ip-172-31-23-216
.
.
Manager Status:
 Address:		13.124.82.171:2377
 Raft Status:		Reachable
 Leader:		No
```

## more detail
```
$ docker info
.
.
Swarm: active
 NodeID: q6f7b4bghbmwjmdud2p4bly7v
 Is Manager: true
.
.
 Node Address: 13.125.178.3
 Manager Addresses:
  13.124.82.171:2377
  13.125.178.3:2377
```

---

## Leader Down

 > Tip : $ docker service rm ping

- make service
```
$ docker service create --name ping --replicas 2 alpine ping docker.com

jmnwuz0yw4tkvqr87vzunaib2
overall progress: 2 out of 2 tasks 
1/2: running   [==================================================>] 
2/2: running   [==================================================>] 
verify: Service converged 
```
- check service
```
$ docker service ps ping

ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR       
zfsy5r0ghvoo        ping.1              alpine:latest       ip-172-31-20-162    Running             Running 45 seconds ago              
sc47jfrfcxkr        ping.2              alpine:latest       ip-172-31-23-216    Running             Running 45 seconds ago            
```
