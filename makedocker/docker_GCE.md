# Make dockerswarm

*Hello. Do you want to make docker-swarm?
Follow this process!
It will improve your skills.*

-----

## Make Google Compute Engine(GCE) 

If you want to do this, you need to create a GCP (Google Cloud Platform)!


- Make instance

| Name                  | Machine type        | Access scopes     | Firewall |
|-------------------    | -------------------| -------------------| -------------------|
| master1  | 1 CPU, 3.75 GB, Ubuntu 18.04 LTS | Allow full access | Allow HTTP,HTTPS |
| worker1  | 1 CPU, 3.75 GB, Ubuntu 18.04 LTS | Allow full access | Allow HTTP,HTTPS |
| worker2  | 1 CPU, 3.75 GB, Ubuntu 18.04 LTS | Allow full access | Allow HTTP,HTTPS |

- Check instaces
```
$ gcloud compute instances list

NAME      ZONE           MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
manager1  us-central1-a  n1-standard-1               10.128.0.2   35.224.82.148   RUNNING
worker1   us-central1-a  n1-standard-1               10.128.0.3   34.67.76.204    RUNNING
worker2   us-central1-a  n1-standard-1               10.128.0.4   35.184.237.189  RUNNING

```

## Make swarm
```
$ gcloud compute ssh manager1

Using username "user".
Authenticating with public key "MZ01-SONDAYEON\user@MZ01-SONDAYEON"
Welcome to Ubuntu 18.04.2 LTS (GNU/Linux 4.15.0-1037-gcp x86_64)
.
.
.
Last login: Fri Aug 16 06:39:07 2019 from 121.134.158.33

```
## Install docker & docker-compose 
```
$ sudo snap install docker
.
.
$ docker -v
Docker version 18.06.1-ce, build e68fc7a

$ docker-compose -v
docker-compose version 1.22.0, build unknown
```

## Initialization
```
$ sudo docker swarm init --advertise-addr [your-master1-Public-IP]
.
.
Swarm initialized: current node (v7vmfaovy835x68mgu01r1es9) is now a manager.
To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-0vske328qpeaq4rg5nl83ad7ereqxm9hhpjq9yzrv9i37aaw0f-f1f1vkw2807rsk3j2irhqlaa4 35.224.82.148:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```
## Check swarm mode 
```
$ docker info
.
.
.
Swarm: active
 NodeID: v7vmfaovy835x68mgu01r1es9
 Is Manager: true
 ClusterID: xvdrp8t0nsu9gzv1b11rwpmll
 Managers: 1
 Nodes: 1
 Orchestration:
  Task History Retention Limit: 5
 Raft:
  Snapshot Interval: 10000
  Number of Old Snapshots to Retain: 0
  Heartbeat Tick: 1
  Election Tick: 10
 Dispatcher:
  Heartbeat Period: 5 seconds
 CA Configuration:
  Expiry Duration: 3 months
  Force Rotate: 0
 Autolock Managers: false
 Root Rotation In Progress: false
 Node Address: 35.224.82.148
 Manager Addresses:
  35.224.82.148:2377
```
## Join

```
$ sudo docker swarm join-token worker
.
.
To add a worker to this swarm, run the following command:
    docker swarm join --token SWMTKN-1-0vske328qpeaq4rg5nl83ad7ereqxm9hhpjq9yzrv9i37aaw0f-f1f1vkw2807rsk3j2irhqlaa4 35.224.82.148:2377
```
## Connect to Worker1
```
$ gcloud compute ssh worker1

Welcome to Ubuntu 18.04.2 LTS (GNU/Linux 4.15.0-1037-gcp x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
.
.
.
Last login: Fri Aug 16 07:21:33 2019 from 35.224.82.148
```

## Docker swarm join (worker1)
```
$ docker swarm join -- token [your-token-value] [your-manager1-ip]:[port-number]
```
```
root@worker1:~#  docker swarm join --token SWMTKN-1-1y66zg6hsrc8haclmlrrrf7dyczl0ozwr0tgwg4iokoxenmy6k-a5hr1yz3fba67xk4wzkdw5b1t 10.128.0.2:2377
.
.
This node joined a swarm as a worker.
```
## Connect to worker2
```
$ gcloud compute ssh worker2

Welcome to Ubuntu 18.04.2 LTS (GNU/Linux 4.15.0-1037-gcp x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
```
## Docker swarm join (worker2)
```
root@worker2:~#  docker swarm join --token SWMTKN-1-1y66zg6hsrc8haclmlrrrf7dyczl0ozwr0tgwg4iokoxenmy6k-a5hr1yz3fba67xk4wzkdw5b1t 10.128.0.2:2377
.
.
This node joined a swarm as a worker.
```
## Check to Swarm Active
```
root@worker1:~# docker info
.
.
Swarm: active
 NodeID: vhp68ltkciqszo8mk36j6syx6
 Is Manager: false
 Node Address: 10.128.0.3
 Manager Addresses:
  10.128.0.2:2377
```
```
root@worker2:~# docker info
.
.
Swarm: active
 NodeID: f62333wn4ygsemu5ubnmg1gnc
 Is Manager: false
 Node Address: 10.128.0.4
 Manager Addresses:
  10.128.0.2:2377
```
## Check node
```
$ docker node ls

ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
s29azx34toxyylnrtvtf6c02q *   manager1            Ready               Active              Leader              18.06.1-ce
vhp68ltkciqszo8mk36j6syx6     worker1             Ready               Active                                  18.06.1-ce
f62333wn4ygsemu5ubnmg1gnc     worker2             Ready               Active                                  18.06.1-ce
```
