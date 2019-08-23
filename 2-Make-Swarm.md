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
