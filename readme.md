# Personal repo to setup a new VPS for my own use.

Sometimes I buy a new VPS and need setup everytime, so I made self tutorial for that use case. I just wanna do somethings like
- add a new users and add to sudo group
- install docker
- install portainer
- install nginx proxy manager
- install monitoring tools (grafana+prometheus+node exporter+cadvisor)

this tutorial run on Debian 11 with min RAM 2GB (monitoring tools is kind a heavy)

- add user
  ```terminal
  sudo adduser -m jfraziz
  sudo usermod -aG sudo jfraziz
  ```

- set permit login no
  ```terminal
  sudo vi /etc/ssh/sshd_config
  ```
  
  ```bash
  # other config
  
  PermitRootLogin no
  
  # other config
  ```
  
  ```terminal
  sudo systemctl restart sshd
  ```

- install docker (debian)
  ```terminal
  sudo apt-get update & sudo apt-get upgrade

  sudo apt-get install \
      ca-certificates \
      curl \
      gnupg \
      lsb-release

  curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

  echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

  sudo apt-get update

  sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

  sudo systemctl enable docker
  ```
  
  add user to docker group
  
  ```terminal
  sudo groupadd docker
  sudo usermod -aG docker $USER
  ```

- install nginx-proxy-manager & portainer, just run this [docker-compose.yml](./docker-compose.yml) with `docker-compose up -d`

  ```terminal
  docker compose up -d
  ```
  
  forward port to setup nginx proxy manager, upload cloudflare cert, and etc
  
  ```terminal
  ssh -L 9000:127.0.0.1:9000 -N -p 22 jfraziz@<ip>
  ssh -L 8081:127.0.0.1:81 -N -p 22 jfraziz@<ip>
  ```

  remember you must setup firewall for port you want expose (in my case I just export 22, 80, and 443, DigitalOcean or Linode has tools for it).

- run monitoring tools

  just run [docker-compose.yml](./monitoring/docker-compose.yml) on monitoring folder.

  ```
  docker compose  up -d 
  ```
