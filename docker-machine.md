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
  
