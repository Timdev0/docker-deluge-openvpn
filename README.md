# OpenVPN and Deluge with WebUI

## Acknowledgments

This is a fork of [ebrianne repo](https://github.com/ebrianne/docker-deluge-openvpn) I just updated the ubuntu and deluge version. 
This project is based heavily on the fork of [docker-transmission-openvpn](https://github.com/haugene/docker-transmission-openvpn). All VPN configurations are now moved to a [separate repository](https://github.com/haugene/vpn-configs-contrib).

## Quick Start

This container contains OpenVPN and Deluge with a configuration
where Deluge is running only when OpenVPN has an active tunnel.
It bundles configuration files for many popular VPN providers to make the setup easier.

```
$ docker run --cap-add=NET_ADMIN -d \
             --sysctl=net.ipv6.conf.all.disable_ipv6=1 \
              -v /your/storage/path/to/downloads/:/download \
              -v /your/storage/path/to/config/:/config \
              -e OPENVPN_PROVIDER=PIA \
              -e OPENVPN_CONFIG=France \
              -e OPENVPN_USERNAME=user \
              -e OPENVPN_PASSWORD=pass \
              -e LOCAL_NETWORK=192.168.0.0/16 \
              -e DELUGE_INCOMPLETE_DIR=/download \
              -e DELUGE_MOVE_COMPLETED=false \
              -e DELUGE_COPY_TORRENT=false \
              -e DELUGE_WEB_PORT=8112 \
              -e DELUGE_DEAMON_PORT=58846 \
              -p 8112:8112 \
              timdev0/docker-deluge-openvpn:master
```

## Docker Compose
```
version: '3.2'
services:
    deluge-openvpn:
        volumes:
            - '/your/storage/path/to/downloads/:/download'
            - '/your/storage/path/to/config/:/config'
        environment:
          TZ: ${TIMEZONE}
          PUID: ${USER_ID}
          PGID: ${GROUP_ID}
          OPENVPN_PROVIDER: PIA
          OPENVPN_CONFIG: switzerland
          OPENVPN_USERNAME: ${VPN_USER}
          OPENVPN_PASSWORD: ${VPN_PASS}
          LOCAL_NETWORK: 192.168.0.0/16
          DELUGE_INCOMPLETE_DIR: /download # or /dowload/incomplete
          DELUGE_MOVE_COMPLETED: false
          #DELUGE_DOWNLOAD_DIR: /download/completed #optional put DELUGE_MOVE_COMPLETED to true
          DELUGE_COPY_TORRENT: false
          # DELUGE_TORRENT_DIR: /download/torrents #optional put COPY_TORRENT_FILE to true
          # DELUGE_WATCH_DIR: /download/watch #optional
          # DELUGE_WEB_PORT: 8112 #optinal
          # DELUGE_DEAMON_PORT: 58846 #optional  
        cap_add:
            - NET_ADMIN
        sysctls:
            - net.ipv6.conf.all.disable_ipv6=1
        ports:
            - '8112:8112'
        image: timdev0/docker-deluge-openvpn:master
```
## Documentation

The documentation for this image is hosted on GitHub pages:

https://ebrianne.github.io/docker-deluge-openvpn/

## Access the WEB UI
Access http://HOSTIP:PORT from a browser on the same network. Default password is `deluge`.

## Local Client Access
If you want to access Deluge from a Local client other than the WEB UI, like [Trieme for Android App](https://f-droid.org/packages/org.deluge.trireme/):
Edit the file `/your/storage/path/to/config/auth` to add a new line `username:password:10`, save changes and restart container.

| Credential | Default Value |
| ---------- | ------------- |
| `Host`     | HOST IP       |
| `Port`     | 58846         |
| `Username` | username      |
| `Password` | password      |
