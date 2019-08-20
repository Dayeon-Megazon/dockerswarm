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
  ```
  $ sdocker-machine create node1
  
  Running pre-create checks...
  Error with pre-create check: "This computer doesn't have VT-X/AMD-v enabled. Enabling it in the BIOS is mandatory"
  ```

