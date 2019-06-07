# Ansible Testing

This image is to test functionality of Ansible from within a container. It is aimed at being used only with CI tests of Ansible roles or playbooks.

[![Docker Pulls](https://img.shields.io/docker/pulls/willhallonline/ansible-test.svg)][hub] [![](https://images.microbadger.com/badges/image/willhallonline/ansible-test.svg)](https://microbadger.com/images/willhallonline/ansible-test "Get your own image badge on microbadger.com") [![Docker Automated build](https://img.shields.io/docker/automated/willhallonline/ansible-test.svg)][hub] [![Docker Build Status](https://img.shields.io/docker/build/willhallonline/ansible-test.svg)][hub]

## Supported tags and respective `Dockerfile` links

### Ansible 2.8 (with Mitogen)

* `latest`, `ubuntu`, `2.8-ubuntu`, `ubuntu-18.04`, `2.8-ubuntu-18.04` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible28/ubuntu1804/Dockerfile)
* `ubuntu-16.04`, `2.8-ubuntu-16.04` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible28/ubuntu1604/Dockerfile)
* `centos`, `centos-7`, `2.8-centos`, `2.8-centos-7` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible28/centos7/Dockerfile)
* `stretch`, `2.8-stretch` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible28/stretch/Dockerfile)
* `stretch-slim`, `2.8-stretch-slim` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible28/stretch-slim/Dockerfile)

### Ansible 2.7 (with Mitogen)

* `2.7-ubuntu`, `ubuntu-18.04`, `2.7-ubuntu-18.04` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible27/ubuntu1804/Dockerfile)
* `2.7-ubuntu-16.04` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible27/ubuntu1604/Dockerfile)
* `2.7-centos`, `2.7-centos-7` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible27/centos7/Dockerfile)
* `2.7-stretch` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible27/stretch/Dockerfile)
* `2.7-stretch-slim` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible27/stretch-slim/Dockerfile)

### Ansible 2.5 (with Mitogen)

**Ansible 2.5 is end of life and therefore deprecated**

## Mitogen

To leverage *Mitogen* to accelerate your playbook runs, add this to your `ansible.cfg`:

```
strategy_plugins = /usr/lib/python2.7/site-packages/ansible_mitogen/plugins/strategy
strategy = mitogen_linear
```

## Running

**You will likely need to mount required directories into your container to make it run (or build on top of what is here).**

### Simple

```
$   docker run --rm -it willhallonline/ansible-test /bin/sh
```

### Mount local directory and ssh key

```
$   docker run --rm -it -v $(pwd):/ansible -v ~/.ssh/id_rsa:/root/id_rsa willhallonline/ansible-test:ubuntu /bin/sh
```

### Injecting commands

```
$   docker run --rm -it -v $(pwd):/ansible -v ~/.ssh/id_rsa:/root/id_rsa willhallonline/ansible-test:ubuntu ansible-playbook playbook.yml
```

## Maintainer

* Written and maintained by Will Hall (https://www.willhallonline.co.uk)

[hub]: https://hub.docker.com/r/willhallonline/ansible-test
[microbadger]: https://microbadger.com/images/willhallonline/ansible-test
