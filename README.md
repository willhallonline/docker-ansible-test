# Ansible Testing

Docker images with **systemd** enabled for testing Ansible roles and playbooks inside containers. These images extend [willhallonline/ansible](https://github.com/willhallonline/docker-ansible) with systemd support, making them ideal for CI/CD testing of roles that manage services or interact with the init system.

[![Docker Pulls](https://img.shields.io/docker/pulls/willhallonline/ansible-test "Docker Pulls")][hub] ![Docker Image Size (tag)](https://img.shields.io/docker/image-size/willhallonline/ansible-test/latest)

## Current Ansible Core Versions

These are the latest Ansible Core versions running within the containers:

- Ansible 2.16: 2.16.14
- Ansible 2.17: 2.17.14
- Ansible 2.18: 2.18.9
- Ansible 2.19: 2.19.2
- Ansible 2.20: 2.20.0
- Ansible 2.21: 2.21.0

## Supported tags and respective `Dockerfile` links

### Ansible Core (2.16, 2.17, 2.18, 2.19, 2.20, 2.21)

These images include:

- `ansible-core` (<https://pypi.org/project/ansible-core/>)
- `ansible` (<https://pypi.org/project/ansible/>)
- `ansible-lint` (<https://pypi.org/project/ansible-lint/>)
- **systemd** for service management testing

ARM (ARM64/ARMv7) releases are available for all container images.

| Base Image (↓) \ Ansible Version (→) | Dockerfile                                                                                                               | 2.21                          | 2.20                          | 2.19                          | 2.18                          | 2.17                          | 2.16                          |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------|-------------------------------|-------------------------------|-------------------------------|-------------------------------|-------------------------------|-------------------------------|
| Bookworm (Debian 12)                 | [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible-core/debian-bookworm/Dockerfile)      |                           | | `2.19-debian-bookworm` | `2.18-debian-bookworm` | `2.17-debian-bookworm` | `2.16-debian-bookworm` |
| Bookworm Slim (Debian 12)            | [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible-core/debian-bookworm-slim/Dockerfile) |                           | | `2.19-debian-bookworm-slim` | `2.18-debian-bookworm-slim` | `2.17-debian-bookworm-slim` | `2.16-debian-bookworm-slim` |
| Trixie (Debian 13)                   | [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible-core/debian-trixie/Dockerfile)        | `latest`, `2.21-debian-trixie` | `2.20-debian-trixie` | `2.19-debian-trixie` | `2.18-debian-trixie` | `2.17-debian-trixie` | |
| Trixie Slim (Debian 13)              | [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible-core/debian-trixie-slim/Dockerfile)   | `2.21-debian-trixie-slim` | `2.20-debian-trixie-slim` | `2.19-debian-trixie-slim` | `2.18-debian-trixie-slim` | `2.17-debian-trixie-slim` | |
| Rocky Linux 10                       | [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible-core/rockylinux-10/Dockerfile)        | `2.21-rockylinux-10` | `2.20-rockylinux-10` | `2.19-rockylinux-10` | `2.18-rockylinux-10` | `2.17-rockylinux-10` | `2.16-rockylinux-10` |
| Ubuntu 22.04                         | [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible-core/ubuntu-22.04/Dockerfile)         | | | | | `2.17-ubuntu-22.04` | `2.16-ubuntu-22.04` |
| Ubuntu 24.04                         | [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible-core/ubuntu-24.04/Dockerfile)         | `2.21-ubuntu-24.04` | `2.20-ubuntu-24.04` | `2.19-ubuntu-24.04` | `2.18-ubuntu-24.04` | `2.17-ubuntu-24.04` | `2.16-ubuntu-24.04` |
| Ubuntu 26.04                         | [Dockerfile](https://github.com/willhallonline/docker-ansible-test/blob/master/ansible-core/ubuntu-26.04/Dockerfile)         | `2.21-ubuntu-26.04` | `2.20-ubuntu-26.04` | | | | |

> **Note:** Alpine images are not provided as Alpine uses OpenRC, not systemd.

## Running

These containers run systemd as PID 1 and require either `--privileged` flag or explicit cgroup mounts to function properly.

### Interactive shell with systemd

```bash
docker run --rm -it --privileged willhallonline/ansible-test:latest /bin/bash
```

### Testing an Ansible role (with systemd support)

```bash
docker run --rm --privileged \
  -v /sys/fs/cgroup:/sys/fs/cgroup:rw \
  -v $(pwd):/ansible \
  willhallonline/ansible-test:2.21-debian-trixie \
  ansible-playbook /ansible/playbook.yml
```

### CI/CD Usage

#### GitHub Actions Service Container

```yaml
services:
  target:
    image: willhallonline/ansible-test:2.21-ubuntu-24.04
    options: --privileged
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:rw
```

#### Using with molecule

```ini
# molecule/default/molecule.yml
provisioner:
  name: ansible
  options:
    vvv: true

platforms:
  - name: instance
    image: willhallonline/ansible-test:2.21-debian-trixie
    pre_build_image: true
    privileged: true
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:rw
    docker_networks:
      - name: ansible_test
```

### Bash Alias

You can put these inside your dotfiles (~/.bashrc or ~/.zshrc).

```bash
alias docker-ansible-test='docker run --rm -it --privileged -v /sys/fs/cgroup:/sys/fs/cgroup:rw -v $(pwd):/ansible willhallonline/ansible-test:latest /bin/bash'
alias docker-ansible-test-play='docker run --rm --privileged -v /sys/fs/cgroup:/sys/fs/cgroup:rw -v $(pwd):/ansible willhallonline/ansible-test:latest ansible-playbook'
```

Use with:

```bash
docker-ansible-test
docker-ansible-test-play /ansible/playbook.yml
```

## Building Locally

```bash
docker build \
  --build-arg ANSIBLE_TAG=2.21-debian-trixie \
  -t ansible-test:2.21-debian-trixie \
  ansible-core/debian-trixie/
```

## Important Notes

### Systemd Container Requirements

- Containers must run with `--privileged` or explicit cgroup mounts (`-v /sys/fs/cgroup:/sys/fs/cgroup:rw`)
- The container starts systemd as PID 1, allowing `service` and `systemctl` commands to work
- Useful for testing roles that:
  - Enable/disable/start/stop services
  - Use `systemd` module
  - Interact with systemd timers or targets

### Running Services

When the container starts (without overriding CMD), systemd will start and manage services:

```bash
docker run --rm --privileged \
  -v /sys/fs/cgroup:/sys/fs/cgroup:rw \
  willhallonline/ansible-test:2.21-debian-trixie
```

You can then use `docker exec` to run ansible commands against the running container:

```bash
docker exec <container-id> ansible-playbook /ansible/playbook.yml
```

## Maintainer

- Written and maintained by [Will Hall](https://www.willhallonline.co.uk)

[hub]: https://hub.docker.com/r/willhallonline/ansible-test
