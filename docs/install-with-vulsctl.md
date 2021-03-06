---
id: install-with-vulsctl
title: Install with vulsctl
sidebar_label: Vulsctl - Quickest Vuls setup
---

## Vulsctl

### Linux Distributions

The following example should work on Fedora based Linux distributions,
which include: CentOS, RedHat, Amazon Linux etc (tested on CentOS and
Red Hat 7).

[Vulsctl](https://github.com/vulsio/vulsctl) was created to ease setup. Each
shell script is a wrapper around Docker commands.

## Setup Docker

- Install [Docker](https://docs.docker.com/engine/install/)
- [Manage Docker as a non-root user](https://docs.docker.com/install/linux/linux-postinstall/)

```bash
$ sudo systemctl start docker
```

## Clone Vulsctl

```bash
$ git clone https://github.com/vulsio/vulsctl.git
$ cd vulsctl
```

## Fetch Vulnerability Databases

This will take some time ...

```bash
$ cd docker
$ ./update-all.sh
```

To fetch Vulnerability database for Japanese(JVN).

```bash
$ ./jvn.sh
```

## Config, Scan, Report

Prepare the **config.toml** in the **vulsctl** install directory similar to
the configuration below.

```toml
[servers]
[servers.hostos]
host        = "52.10.10.10"
port        = "22"
user        = "centos"
# if ssh config file exists in .ssh, path to ssh config file in docker
sshConfigPath   = "/root/.ssh/config"
# keypath in the Vuls docker container
keyPath     = "/root/.ssh/id_rsa"
```

When `config` exists in `.ssh`, vuls refers to `/root/.ssh/config` in the docker container when connecting to SSH.
However, an error occurs because the local user does not match the user in Docker.
To deal with this, specify `/root/.ssh/config` in `sshConfigPath`.

The **scan.sh** will mount **$HOME/.ssh** from the host operating system into
the Docker container, however you will need to SSH into the target server
beforehand which will add your fingerprint to $HOME/.ssh/known_hosts.

`
![scan](https://user-images.githubusercontent.com/534611/66093182-20535f00-e5ca-11e9-8060-8c9247abcefa.jpg)

```bash
$ ssh centos@52.100.100.100 -i ~/.ssh/id_rsa.pem
```

```bash
$ ./scan.sh
$ ./report.sh
$ ./tui.sh
```

For details, see

- [scan.sh](https://github.com/vulsio/vulsctl/blob/master/docker/scan.sh)
- [report.sh](https://github.com/vulsio/vulsctl/blob/master/docker/report.sh)
- [tui.sh](https://github.com/vulsio/vulsctl/blob/master/docker/tui.sh)

## Vulsrepo

```bash
$ ./vulsrepo.sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
39c8830dbeac        ishidaco/vulsrepo   "vulsrepo-server"   3 seconds ago       Up 1 second         0.0.0.0:5111->5111/tcp   focused_wu
```

Vulsrepo is running on `http://host-ip:5111`.
