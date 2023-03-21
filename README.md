# forticlient

Connect to a FortiNet VPNs through docker

## Run

The container uses the `openfortivpn` linux binary to manage ppp interface

All the container traffic is routed through the VPN, so you can in turn route host traffic through the container to
access remote subnets.

### With docker-compose (Default)

For run with docker compose you can use bellow steps:

1. Copy `env/vpn/.env.example` to `env/vpn/.env`
2. Fill your vpn identity on `env/vpn/.env`
3. Execute bellow command for run **forticlient** and **socks5**

```bash
### Without publish port
### Run two container: 1. forticlient 2.socks5
docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  up -d

### With publish port on 127.0.0.1
### socks5 server: 127.0.0.1:1080
docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  -f docker/docker-compose.publish.yml \
  up -d
```

Also, you can run with own compose config. You can create a config compose file on `docker/custom` folder. This folder
not tracked with source control.

**Tip:** You can add custom port for socks5 server without change in any compose file with below environment
variable:

* FORTI_SOCKS_PORT (Default: 1080)

```bash
### With publish port on 127.0.0.1
### socks5 server: 127.0.0.1:8080
FORTI_SOCKS_PORT=8080 docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  -f docker/docker-compose.publish.yml \
  up -d
```

### With docker-compose (ssh server)

If you want using ssh server follow bellow steps:

1. Copy `env/vpn/.env.example` to `env/vpn/.env`
2. Fill your vpn identity on `env/vpn/.env`
3. Copy `env/ssh/.env.example` to `env/ssh/.env`
4. Fill you ssh requirement authenticate on `env/ssh/.env`
5. Execute bellow command for run **forticlient** and **ssh** and **socks5**

```bash
### Without publish port
### Run three container: 1. forticlient 2.socks5 3, ssh
docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  -f docker/docker-compose.ssh.yml \
  -f docker/docker-compose.ssh-env.yml \
  up -d

### With publish port on 127.0.0.1
### socks5 server: 127.0.0.1:1080
### ssh server: 127.0.0.1:2222
docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  -f docker/docker-compose.publish.yml \
  -f docker/docker-compose.ssh.yml \
  -f docker/docker-compose.ssh-env.yml \
  -f docker/docker-compose.ssh-publish.yml \
  up -d
```

Also, you can run with own compose config. You can create a config compose file on `docker/custom` folder. This folder
not tracked with source control.

**Tip:** You can add custom port for ssh and socks5 server withou change in any compose file with two environment
variable:

* FORTI_SSH_PORT (Default: 2222)
* FORTI_SOCKS_PORT (Default: 1080)

```bash
### With publish port on 127.0.0.1
### socks5 server: 127.0.0.1:8080
FORTI_SSH_PORT=2020 FORTI_SOCKS_PORT=8080 docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  -f docker/docker-compose.publish.yml \
  -f docker/docker-compose.ssh.yml \
  -f docker/docker-compose.ssh-env.yml \
  -f docker/docker-compose.ssh-publish.yml \
  up -d
```

### With docker-compose (port forwarder)

If you want using ssh server follow bellow steps:

1. Copy `env/vpn/.env.example` to `env/vpn/.env`
2. Fill your vpn identity on `env/vpn/.env`
3. Execute bellow command for run **forticlient** and **socks5** and **socat**

```bash
### Without publish port
### Run three container: 1. forticlient 2.socks5 3, socat
FORTI_SOCAT_DEST_ADDR=remote-addr FORTI_SOCAT_DEST_PORT=3389 docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  -f docker/docker-compose.socat.yml \
  up -d

### With publish port on 127.0.0.1
### socks5 server: 127.0.0.1:1080
### socat server: 127.0.0.1:3389
FORTI_SOCAT_PORT=3389 FORTI_SOCAT_DEST_ADDR=remote-addr FORTI_SOCAT_DEST_PORT=3389 docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  -f docker/docker-compose.publish.yml \
  -f docker/docker-compose.socat.yml \
  -f docker/docker-compose.socat-publish.yml \
  up -d
```

Also, you can run with own compose config. You can create a config compose file on `docker/custom` folder. This folder
not tracked with source control.

**Tip:** You can add custom port for socat and socks5 server without change in any compose file with two environment
variable:

* FORTI_SOCAT_PORT (Default: 3389)

```bash
### With publish port on 127.0.0.1
### socat server: 127.0.0.1:3389
FORTI_SOCAT_DEST_ADDR=remote-addr FORTI_SOCAT_DEST_PORT=3389 FORTI_SOCAT_PORT=3389 docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  -f docker/docker-compose.publish.yml \
  -f docker/docker-compose.ssh.yml \
  -f docker/docker-compose.ssh-env.yml \
  -f docker/docker-compose.ssh-publish.yml \
  up -d
```

### With multi vpn instance

If you want using multi vpn instance with single compose file, You should follow bellow steps:

For run with docker compose you can use bellow steps:

1. Copy `env/vpn/.env.example` to `env/vpn/vpn-1.env`
2. Fill your vpn identity on `env/vpn/vpn-1.env`
3. Copy `env/vpn/.env.example` to `env/vpn/vpn-2.env`
4. Fill your vpn identity on `env/vpn/vpn-2.env`
5. You have to fill variable `FORTI_VPN_ENV`, It is path of env file you want use it
6. Execute bellow command for run **forticlient** and **socks5**

```bash
### Without publish port
### Run VPN-1
COMPOSE_PROJECT_NAME=vpn-1 FORTI_VPN_ENV=env/vpn/vpn-1.env docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  up -d

### With publish port on 127.0.0.1
### Run VPN-1 with socks5 publish port on port 8080
COMPOSE_PROJECT_NAME=vpn-1 FORTI_VPN_ENV=env/vpn/vpn-1.env FORTI_SOCKS_PORT=8080 docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  -f docker/docker-compose.publish.yml \
  up -d
  
##############################################################

### Without publish port
### Run VPN-2
COMPOSE_PROJECT_NAME=vpn-2 FORTI_VPN_ENV=env/vpn/vpn-2.env docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  up -d

### With publish port on 127.0.0.1
### Run VPN-2 with socks5 publish port on port 8081
COMPOSE_PROJECT_NAME=vpn-2 FORTI_VPN_ENV=env/vpn/vpn-2.env FORTI_SOCKS_PORT=8081 docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  -f docker/docker-compose.publish.yml \
  up -d
```

You can do same for port forwarder container:

```bash
### Run VPN-1 and forwarder RDP port to remote-server-1
### socks5 server: 127.0.0.1:1080
### socat server: 127.0.0.1:3389
COMPOSE_PROJECT_NAME=vpn-1 FORTI_VPN_ENV=env/vpn/vpn-1.env FORTI_SOCKS_PORT=8080 FORTI_SOCAT_PORT=3389 FORTI_SOCAT_DEST_ADDR=remote-server-1 FORTI_SOCAT_DEST_PORT=3389 docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  -f docker/docker-compose.publish.yml \
  -f docker/docker-compose.socat.yml \
  -f docker/docker-compose.socat-publish.yml \
  up -d
  
### Run VPN-2 and forwarder RDP port to remote-server-2
### socks5 server: 127.0.0.1:1081
### socat server: 127.0.0.1:3390
COMPOSE_PROJECT_NAME=vpn-2 FORTI_VPN_ENV=env/vpn/vpn-2.env FORTI_SOCKS_PORT=1081 FORTI_SOCAT_PORT=3390 FORTI_SOCAT_DEST_ADDR=remote-server-2 FORTI_SOCAT_DEST_PORT=3389 docker-compose \
  -f docker-compose.yml \
  -f docker/docker-compose.env.yml \
  -f docker/docker-compose.publish.yml \
  -f docker/docker-compose.socat.yml \
  -f docker/docker-compose.socat-publish.yml \
  up -d
```

### Run forti client container

```bash
docker run -it \
  --device /dev/net/tun:/dev/net/tun \
  --device /dev/ppp:/dev/ppp \
  --cap-add NET_ADMIN \
  -e VPN_ADDR=host:port \
  -e VPN_USER=me@domain \
  -e VPN_PASS=secret \
  poyaz/forticlient:latest
```

### Run forti client container (With port forwarder)

```bash
docker run -it \
  --name your-vpn-container-name \
  --device /dev/net/tun:/dev/net/tun \
  --device /dev/ppp:/dev/ppp \
  --cap-add NET_ADMIN \
  -p 3389:3389 \
  -e VPN_ADDR=host:port \
  -e VPN_USER=me@domain \
  -e VPN_PASS=secret \
  poyaz/forticlient:latest

### RDP port forwarder
docker run -it \
  --name your-forwarder-container-name \
  --network container:your-vpn-container-name \
  alpine/socat:latest tcp4-listen:3389,reuseaddr tcp:your-rdp-destination-addr:3389
```

## Two-factor authentication

This image now support two-factor authentication. List of support 2Fa in below:

### Manual 2Fa (With a file)

The code listen the file and after write OTP on file you authenticate processed. Because we are on a container and don't
have GUI

If your server using 2Fa, after the vpn authenticated successfully create lock file on `storage/docker/2fa` directory
with name `storage/docker/2fa/2fa.txt` (If you use default variable for 2Fa), then you should put your OTP in this
file, After you write your OTP in this file your authenticate has continued. if your OTP code is correct you vpn connect successfully.

**Tip:**
You can change volume with below variable if you want to use another directory or file on a **container**, Then you
should mount this folder on docker-compose file or `docker run` command

* VPN_2FA_DIR (Default: /tmp/2fa/)
* VPN_2FA_FILE (Default: /tmp/2fa/2fa.txt)

```bash
docker run -it \
  --device /dev/net/tun:/dev/net/tun \
  --device /dev/ppp:/dev/ppp \
  --cap-add NET_ADMIN \
  -v /tmp/my-custom-dir:/app
  -e VPN_ADDR=host:port \
  -e VPN_USER=me@domain \
  -e VPN_PASS=secret \
  -e VPN_2FA_DIR=/app \
  -e VPN_2FA_DIR=/app/my-2fa.txt
  poyaz/forticlient:latest
```

## Usage

### With ssh jump

If you want to connect another server you can use ssh jump with below sample:

```bash
### If use docker/docker-compose.publish.yml
ssh -o ProxyCommand="ssh -W %h:%p -p 2222 forti@127.0.0.1" <username>@<target-host>

### If don't use docker/docker-compose.publish.yml
### get container ip with `docker inspect --format '{{ .NetworkSettings.IPAddress }}' <container>`
ssh -o ProxyCommand="ssh -W %h:%p -p 2222 forti@<container-ip>" pouya.azarpour@<target-host>
```

If your ssh server has been removed, and you create new ssh server (or recreated). You
have `Error: forwarding disabled due to host key check failure` error and can fix with two way:

1. You have to remove previous host key from `~/.ssh/known_hosts`
2. You can use bellow command for a skip ssh host key (Because we trust to this server)

```bash
ssh -o ProxyCommand="ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no -W %h:%p -p 2222 forti@127.0.0.1" <username>@<target-host>
```

### With socks5 tunnel

Another way we can use is socks5 tunnel. We can use this feature for connect to server or use for http/https request to
server

```bash
### If use docker/docker-compose.publish.yml
ssh -oProxyCommand="nc -X 5 -x 127.0.0.1:1080 %h %p" <username>@<target-host>

### If don't use docker/docker-compose.publish.yml
### get container ip with `docker inspect --format '{{ .NetworkSettings.IPAddress }}' <container>`
ssh -o ProxyCommand="nc -X 5 -x 127.0.0.1:1080 %h %p" pouya.azarpour@<target-host>
```

### With ip route

If you want to use ip route at first you should find container ip

```bash
### First find container ip
docker inspect --format '{{ .NetworkSettings.IPAddress }}' <container>

ip route add 10.201.0.0/16 via <container-ip>

ssh 10.201.8.1
```

### With ip route (Own network)

```bash
# Create a docker network, to be able to control addresses
docker network create --subnet=172.20.0.0/16 fortinet

docker run -it \
  --device /dev/net/tun:/dev/net/tun \
  --device /dev/ppp:/dev/ppp \
  --cap-add NET_ADMIN \
  --net your-network-name --ip 172.20.0.2 \
  -e VPN_ADDR=host:port \
  -e VPN_USER=me@domain \
  -e VPN_PASS=secret \
  poyaz/forticlient:latest

# Add route for you remote subnet (ex. 10.201.0.0/16)
ip route add 10.201.0.0/16 via 172.20.0.2

# Access remote host from the subnet
ssh 10.201.8.1
```

## Misc

If you don't want to use a docker network, you can find out the container ip once it is started with:

```bash
# Find out the container IP (You should get container with name "vpn")
docker inspect --format '{{ .NetworkSettings.IPAddress }}' <container>
```
