# Docker for AWS Demo

---

## Setting

[Docker for AWS](https://docs.docker.com/docker-for-aws/#docker-community-edition-ce-for-aws)

- Create stack

1. Select 'AWS용 Docker Community Edition (CE) 배포 (stable)'
2. Select 'Amazon S3 템플릿 URL 지정'
3. Choose Your SSH key

## SSH Connection

1. CloudFormation의 상태가 CREATE_COMPLETE 가 될 때까지 기다린다.
2. Output(출력) 탭에 들어가서 Managers의 목록으로 이동한다.
3. Manager 목록 중 하나를 선택하여 인스턴스에 연결한다.
4. $ chmod 400 [your-key-pair-path]
5. $ ssh -i "key-pair-path" docker@<instance-address>

*example*
```
$ ssh -i "dayeon_docker.pem" docker@ec2-13-124-43-158.ap-northeast-2.compute.amazonaws.com

Welcome to Docker!
```

## Check docker node

> 따로 join하지 않아도 이미 다 연결되어있다.

```
$ docker node ls

ID                            HOSTNAME                                           STATUS              AVAILABILITY        MANAGER STATUS  
pylmiug1xwvc766inm2gqbvb7 *   ip-172-31-8-136.ap-northeast-2.compute.internal    Ready               Active              Leader         
99dfo327d5hhtzl4ruf6aqhkk     ip-172-31-11-255.ap-northeast-2.compute.internal   Ready               Active              Reachable      
actdvferfk0xjukksn2rlaqvi     ip-172-31-13-188.ap-northeast-2.compute.internal   Ready               Active              Unreachable   
```
> Tip: 계속 node의 상태를 확인하고 싶다면, 아래와 같이 입력하면 된다.

```
$ watch -d docker node ls
.
.

Every 2s: docker node ls                                                                                           2019-08-21 05:30:40

ID                            HOSTNAME                                           STATUS              AVAILABILITY        MANAGER STATUS 
pylmiug1xwvc766inm2gqbvb7 *   ip-172-31-8-136.ap-northeast-2.compute.internal    Ready               Active              Leader         
99dfo327d5hhtzl4ruf6aqhkk     ip-172-31-11-255.ap-northeast-2.compute.internal   Ready               Active              Reachable       
actdvferfk0xjukksn2rlaqvi     ip-172-31-13-188.ap-northeast-2.compute.internal   Down                Active                             
```

## Visualizer
```
$ docker service create \
-p 8080:8080 \
--constraint=node.role==manager \
--mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
dockersamples/visualizer
```

Go to http://[your-manager-public-ip]:8080

Then, you can see your docker-swarm

*example*

<div>
<img width="800" src="https://user-images.githubusercontent.com/54167990/63415940-795f9d00-c439-11e9-8771-84aad5bd0ec5.PNG" 
</div>
