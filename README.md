# Ansible Testing

This image is to test the functionality of Ansible from within a container. It is aimed at being used only with CI tests of Ansible roles or playbooks.

[![Docker Pulls](https://img.shields.io/docker/pulls/willhallonline/ansible-test.svg)][hub] [![](https://images.microbadger.com/badges/image/willhallonline/ansible-test.svg)](https://microbadger.com/images/willhallonline/ansible-test)

## Supported tags and respective `Dockerfile` links

### Ansible 2.10

* `latest`, `2.10-ubuntu-20.04` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible210/ubuntu2004/Dockerfile)
* `2.10-ubuntu-18.04` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible210/ubuntu1804/Dockerfile)
* `2.10-centos-7` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible210/centos7/Dockerfile)
* `2.10-centos-8` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible210/centos8/Dockerfile)
* `2.10-stretch` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible210/stretch/Dockerfile)
* `2.10-buster` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible210/buster/Dockerfile)


### Ansible 2.9

* `2.9-ubuntu-20.04` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible29/ubuntu2004/Dockerfile)
* `2.9-ubuntu-18.04` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible29/ubuntu1804/Dockerfile)
* `2.9-centos-7` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible29/centos7/Dockerfile)
* `2.9-centos-8` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible29/centos8/Dockerfile)
* `2.9-stretch` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible29/stretch/Dockerfile)
* `2.9-buster` [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible29/buster/Dockerfile)

## Using Mitogen

To leverage *Mitogen* to accelerate your playbook runs, add this to your ```ansible.cfg```:

Please investigate in your container the location of `ansible_mitogen` (it is different per container). You can do this via:

```bash
your_container="ansible:latest"
docker run --rm -it "willhallonline/${your_container}" /bin/sh -c "find / -type d | grep "ansible_mitogen/plugins" | sort | head -n 1"
```

and then configuring your own ansible.cfg like:

```ini
[defaults]
strategy_plugins = /usr/local/lib/python3.7/site-packages/ansible_mitogen/plugins/
strategy = mitogen_linear
```

## Running

**You will likely need to mount required directories into your container to make it run (or build on top of what is here).**

### Simple

```
$   docker run --rm -it willhallonline/ansible-test:latest /bin/sh
```

### Mount local directory and ssh key

```
$   docker run --rm -it -v $(pwd):/ansible -v ~/.ssh/id_rsa:/root/id_rsa willhallonline/ansible-test:latest /bin/sh
```

### Injecting commands

```
$   docker run --rm -it -v $(pwd):/ansible -v ~/.ssh/id_rsa:/root/id_rsa willhallonline/ansible-test:latest ansible-playbook playbook.yml
```

## Maintainer

* Written and maintained by Will Hall (https://www.willhallonline.co.uk)

[hub]: https://hub.docker.com/r/willhallonline/ansible-test
[microbadger]: https://microbadger.com/images/willhallonline/ansible-test
