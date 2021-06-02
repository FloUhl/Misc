# Ubuntu on RPi 4
## Base config
Install Ubuntu (20.04) on SD-Card
`apt update && apt upgrade`
`apt install lubuntu-desktop`

## Allow usb-otg
- Add 
  ```
  [all]
  dtoverlay=dwc2,dr_mode=peripheral
  ``` 
  to `/boot/firmware/usercfg.txt`
- Add `modules-load=dwc2,g_ether` to the end of cmdline.txt (after rootwait)
- Add `libcomposite` into `/etc/modules`
- install dnsmasq
- Add /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following: `network: {config: disabled}`
- add the usb0 to the netplan config
- add `/etc/dnsmasq.d/usb`
  ```
  interface=usb0
  dhcp-range=10.55.0.2,10.55.0.6,255.255.255.248,1h
  dhcp-option=3
  leasefile-ro
  port=0 
  ```
- add `DNSMASQ_EXCEPT='lo'` to the `/etc/default/dnsmasq` file

## install a vnc-server

`apt install tightvnc`

add /usr/bin/

## Install Docker

Follow guide in [Docker Docs](https://docs.docker.com/engine/install/ubuntu/)

## Code-Server
I deploy the [Code-Server](https://github.com/cdr/code-server) as a docker container pulled from [linuxserver.io](https://github.com/linuxserver/docker-code-server).
To do so simply:
```docker
docker pull ghcr.io/linuxserver/code-server
```

I then created a new directory `~/codeserver` on the Pi where I save the `docker-compose.yaml` file:
```docker
---
version: "2.1"
services:
  code-server:
    image:ghcr.io/linuxserver/code-server
    container-name: code-server
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Vienna
    volumes:
      - /opt/appdata/my_config:/config
    ports:
      - 8443:8443
    restart: unless-stopped
```

Then `systemctl enable docker` and `systemctl enable containerd`.

