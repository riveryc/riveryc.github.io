# Go to Another Country via SSR


I'm currently in China with GFW (google it~), so I'm fully "protected" by Chinese Goverment <- Thanks very much!

<!--more-->

But I still need Google to do something really bad, or for all my Aussie friends, you may need it for US Netflix... :)

This is the list about what you will need to prepare:

- [ ] US VPS (Virtual Private Server): e.g. Azure, AWS or other cloud service provider (whichever stable and cheap)
- [ ] A Linux image (I'm using Centos, but others are fine as we only use Docker)
- [ ] Docker Image: 
- [ ] SSR client
  - [ ] Windows: [Download Here][ssr_windows]
  - [ ] MAC: To be finished
  - [ ] Router: ASUS AC86U (recommand) To be finished

I'll assume that you have already ssh into the server you created in US.

* Install Docker as root
   
```bash
yum install docker -y
```

* Enable service

```bash
systemctl enable docker
systemctl start docker
```

* Pull docker image

```bash
docker pull riveryc/ssr:latest
```
* Or build  your own by using this Dockerfile

```bash
# shadowsocks
#
# VERSION 0.0.3

FROM python:3.7-alpine3.7
MAINTAINER River Yang <river@ryc.one>

# RUN apt-get update && \
#     apt-get install -y python-pip libsodium18
RUN pip install shadowsocks==2.8.2

# Configure container to run as an executable
ENTRYPOINT ["/usr/local/bin/ssserver"]
```

* Run the image with your own config

```bash
# Server side will run image "riveryc/ssr:latest" on port 12355 to serve all requests from anywhere (0.0.0.0) with the password $SSPASSWORD in environment variable which has been declared earlier as "your_own_password". All above port & password can be changed to whatever you want.
# -m is the encryption methods, just use this one...

export SSPASSWORD=your_own_password
docker run -d -p 12355:12355 riveryc/ssr:latest -s 0.0.0.0 -p 12355 -k $SSPASSWORD -m aes-256-cfb
```

* Configure your firewall rule in cloud to enable the port for your VPS to public via External IP: Do this in web console... Allow your port is accessible.
* Configure your local client (Windows): mine is in Japan as it's the fastest node for me right now, you can put your US IP.

<img src="/img/post/20190225/windows_client01.png" class="zoomable">

* Configure your local client (MAC)
* Configure your local client (Router)
* Check your current IP [here](https://www.whatismyip.com/)
* Enable your client (Windows)

<img src="/img/post/20190225/windows_client03.png" class="zoomable">

<img src="/img/post/20190225/windows_client04.png" class="zoomable">

* Check your IP again [now](https://www.whatismyip.com/)


Will update MAC Client tomorrow...

Router... will be the last one as it needs more changes... and I'm 100% sure you will **LOSE** your warranty for your **$200** device, so double think before doing it.


[ssr_windows]:https://github.com/shadowsocks/shadowsocks-windows/releases

