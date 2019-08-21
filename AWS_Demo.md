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
5. $ ssh -i "key-pair-path" docker@<instance-address> -> 사용자 이름을 docker로 수정해야한다.
