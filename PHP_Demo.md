# PHP DEMO

*Creating a Private Registry for Docker Image Management*

---

## Restart Docker
```
# vi /etc/docker/daemon.json

{
  "insecure-registries" : ["manager1.example.com:5000"]
}

# systemctl restart docker
```

## Make registry

```
master1:~$ docker service create --name registry -p 5000:5000 registry

n5h84t7upb5ng5gtxw8ws854k
overall progress: 1 out of 1 tasks 
1/1: running   [==================================================>] 
verify: Service converged 
```
```
$ docker service ls

ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
n5h84t7upb5n        registry            replicated          1/1                 registry:latest     *:5000->5000/tcp
```
```
$ docker service ps registry

ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE           ERROR               PORTS
wkz04onhblws        registry.1          registry:latest     ip-172-31-20-162    Running             Running 3 minutes ago                       
```

## Bulid the Dccker Image
```

```
