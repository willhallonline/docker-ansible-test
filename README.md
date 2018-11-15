# Ansible Testing

This image is to test functionality of Ansible from within a container. It is aimed at being used only with CI tests of Ansible roles or playbooks.

[![Docker Pulls](https://img.shields.io/docker/pulls/willhallonline/ansible-test.svg)][hub] [![](https://images.microbadger.com/badges/image/willhallonline/ansible-test.svg)](https://microbadger.com/images/willhallonline/ansible-test "Get your own image badge on microbadger.com") [![Docker Automated build](https://img.shields.io/docker/automated/willhallonline/ansible-test.svg)][hub] [![Docker Build Status](https://img.shields.io/docker/build/willhallonline/ansible-test.svg)][hub]

## Supported tags and respective ```Dockerfile``` links

### Ansible 2.7

* ```ubuntu```, ```2.7-ubuntu```, ```ubuntu-18.04```, ```2.7-ubuntu-18.04``` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible27/ubuntu1804/Dockerfile) ![MicroBadger Layers](https://img.shields.io/microbadger/layers/willhallonline/ansible/ubuntu.svg)
* ```ubuntu-16.04```, ```2.7-ubuntu-16.04``` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible27/ubuntu1604/Dockerfile)
* ```centos```, ```centos-7```, ```2.7-centos```, ```2.7-centos-7``` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible27/centos7/Dockerfile) ![MicroBadger Layers](https://img.shields.io/microbadger/layers/willhallonline/ansible/centos.svg)

### Ansible 2.5 (with Mitogen)

**This is my preferred install variant mainly due to the performance improvements that Mitogen awards you. You can read more about it inside the [Mitogen for Ansible documentation](https://mitogen.readthedocs.io/en/stable/ansible.html).**

* ```2.5-ubuntu```, ```2.5-ubuntu-18.04``` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible25/ubuntu1804/Dockerfile) ![MicroBadger Layers](https://img.shields.io/microbadger/layers/willhallonline/ansible/2.5-ubuntu.svg)
* ```2.5-ubuntu-16.04``` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible25/ubuntu1604/Dockerfile)
* ```2.5-centos```, ```2.5-centos-7``` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible25/centos7/Dockerfile)

To leverage *Mitogen* to accelerate your playbook runs, add this to your ```ansible.cfg```:

```
strategy_plugins = /usr/lib/python2.7/site-packages/ansible_mitogen/plugins/strategy
strategy = mitogen_linear
```

## Running

**You will likely need to mount required directories into your container to make it run (or build on top of what is here).

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
