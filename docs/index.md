# What is this guide and requisites

## WTF is this?
This guide talks about docker images and containers, the basic commands, how to use docker and docker compose and how to create your own image.

## Requisites
* [Docker](https://www.docker.com/)
* [Docker Compose](https://docs.docker.com/compose/)

You can install docker using:
```bash
$ curl -fsSL https://get.docker.com/ -o get-docker.sh && sudo sh get-docker.sh
```

Or in debian derivade linux, you can also use:
```bash
$ sudo apt install docker docker.io
```

To install docker-compose, use:
```bash
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```

Verify installation of both requisites:
```bash
$ docker -v             # verify currently docker installed version
$ docker-compose -v     # verify currently docker-compose installed version
```