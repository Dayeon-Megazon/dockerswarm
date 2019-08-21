  # Install Docker Machine

  [Docker-Machine Install](https://docs.docker.com/machine/install-machine/)
  
  > Tip: Docker must be installed first.
  
  --------------
  
  ## Docker Machine Binary Path
  
  - Mac OS
  ```
  $ base=https://github.com/docker/machine/releases/download/v0.16.0 &&
  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/usr/local/bin/docker-machine &&
  chmod +x /usr/local/bin/docker-machine
  ```
  
  - Linux
  ```
  $ base=https://github.com/docker/machine/releases/download/v0.16.0 &&
  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&
  sudo install /tmp/docker-machine /usr/local/bin/docker-machine
  ```
  
  - Windows (Git Bash)
  ```
  $ base=https://github.com/docker/machine/releases/download/v0.16.0 &&
  mkdir -p "$HOME/bin" &&
  curl -L $base/docker-machine-Windows-x86_64.exe > "$HOME/bin/docker-machine.exe" &&
  chmod +x "$HOME/bin/docker-machine.exe"
  ```
  
  ## Check 
  ```
  $ docker-machine -v

  docker-machine version 0.13.0, build 9ba6da9
  ```
  --------
  
 ## Docker-machine on AWS EC2 
  
  > Tip: 클라우드 컴퓨팅 서비스(EC2, GCE) 등에서는 대부분 HVM 가상화를 사용하고 있고, 그 자체가 이미 VM 이므로 그들은 Bios 에 접근을 할 수 없다.
          그러므로 아래와 같은 오류가 계속 발생하게 된다.
          
  ## create error
  ```
  $ docker-machine create node1

  Running pre-create checks...
  Error with pre-create check: "VBoxManage not found. Make sure VirtualBox is installed and VBoxManage is in the path" 
  ```
  
  *OR*
  
  ```
  $ docker-machine create node1
  
  Running pre-create checks...
  Error with pre-create check: "This computer doesn't have VT-X/AMD-v enabled. Enabling it in the BIOS is mandatory"
  ```
  ## Make 'AWS-SANDBOX' (명령 줄 플래그를 사용하는 방법)
  
  ```
  $ create --driver amazonec2 --amazonec2-access-key [your-access-key] --amazonec2-secret-key [your-secret-key] aws-sandbox
  ```
  
  *example*
  ```
  $ docker-machine create --driver amazonec2 --amazonec2-access-key AKI###### --amazonec2-secret-key mY6###### aws-sandbox

  Running pre-create checks...
  Creating machine...
  (aws-sandbox) Launching instance...
  .
  .
  Docker is up and running!
  To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env aws-sandbox
  ```
  ## Docker host (Driver 없이 create 하는 방법)
  ```
  $ docker-machine create --driver none --url=tcp://172.31.20.162:2376 custombox
  
  Running pre-create checks...
  Creating machine...
  To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env custombox
  ```
  
  ## Check
  ```
  $ docker-machine ls

  NAME          ACTIVE   DRIVER      STATE     URL                         SWARM   DOCKER     ERRORS
  aws-sandbox   -        amazonec2   Running   tcp://54.166.208.230:2376           v19.03.1   
  custombox     -        none        Running   tcp://172.31.20.162:2376            Unknown    Unable to query docker version: Cannot ... 
  ```
---

## 환경변수 추가하는 방법
  
  [AWS CLI 구성](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html#cli-quick-configuration)
  
  ```
  $ aws configure
  
  AWS Access Key ID [None]: AKI#####...
  AWS Secret Access Key [None]: wJa####....
  Default region name [None]: ap-northeast-2
  Default output format [None]: json
  ```
  > 이 방법을 사용하는 경우엔 바로 create가 가능하다.
  
  *example*
  ```
  $ docker-machine create --driver amazonec2 aws01
  ```
  ```
  $ docker-machine env aws01
  
  export DOCKER_TLS_VERIFY="1"
  export DOCKER_HOST="tcp://34.227.52.179:2376"
  export DOCKER_CERT_PATH="/home/ec2-user/.docker/machine/machines/aws01"
  export DOCKER_MACHINE_NAME="aws01"
  # Run this command to configure your shell: 
  # eval $(docker-machine env aws01)
  ```
