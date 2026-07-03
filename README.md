# Ansible Testing

Ansible testing images with **systemd** enabled inside Docker containers. These images extend [willhallonline/ansible](https://github.com/willhallonline/docker-ansible) with systemd support, making them suitable for CI tests of Ansible roles or playbooks that manage services.

[![Docker Pulls](https://img.shields.io/docker/pulls/willhallonline/ansible-test.svg)][hub] ![Docker Image Size (tag)](https://img.shields.io/docker/image-size/willhallonline/ansible-test/latest)

## Supported tags and respective `Dockerfile` links

Tags follow the pattern: `<ansible-version>-<os>` (e.g. `2.21-debian-trixie`)

### Ansible Core (2.16, 2.17, 2.18, 2.19, 2.20, 2.21)

| Base Image (↓) \ Ansible Version (→) | 2.21 | 2.20 | 2.19 | 2.18 | 2.17 | 2.16 |
|---------------------------------------|------|------|------|------|------|------|
| Debian Bookworm | | | `2.19-debian-bookworm` | `2.18-debian-bookworm` | `2.17-debian-bookworm` | `2.16-debian-bookworm` |
| Debian Bookworm Slim | | | `2.19-debian-bookworm-slim` | `2.18-debian-bookworm-slim` | `2.17-debian-bookworm-slim` | `2.16-debian-bookworm-slim` |
| Debian Trixie | `latest`, `2.21-debian-trixie` | `2.20-debian-trixie` | `2.19-debian-trixie` | `2.18-debian-trixie` | `2.17-debian-trixie` | |
| Debian Trixie Slim | `2.21-debian-trixie-slim` | `2.20-debian-trixie-slim` | `2.19-debian-trixie-slim` | `2.18-debian-trixie-slim` | `2.17-debian-trixie-slim` | |
| Rocky Linux 10 | `2.21-rockylinux-10` | `2.20-rockylinux-10` | `2.19-rockylinux-10` | `2.18-rockylinux-10` | `2.17-rockylinux-10` | `2.16-rockylinux-10` |
| Ubuntu 22.04 | | | | | `2.17-ubuntu-22.04` | `2.16-ubuntu-22.04` |
| Ubuntu 24.04 | `2.21-ubuntu-24.04` | `2.20-ubuntu-24.04` | `2.19-ubuntu-24.04` | `2.18-ubuntu-24.04` | `2.17-ubuntu-24.04` | `2.16-ubuntu-24.04` |
| Ubuntu 26.04 | `2.21-ubuntu-26.04` | `2.20-ubuntu-26.04` | | | | |

> **Note:** Alpine images are not provided as Alpine uses OpenRC, not systemd.

## Running

These containers run systemd as PID 1 and require `--privileged` or appropriate cgroup mounts.

### Interactive shell

```bash
docker run --rm -it --privileged willhallonline/ansible-test:latest /bin/bash
```

### Testing an Ansible role

```bash
docker run --rm --privileged \
  -v /sys/fs/cgroup:/sys/fs/cgroup:rw \
  -v $(pwd):/ansible \
  willhallonline/ansible-test:2.21-debian-trixie \
  ansible-playbook /ansible/playbook.yml
```

### CI usage (e.g. GitHub Actions)

```yaml
services:
  target:
    image: willhallonline/ansible-test:2.21-ubuntu-24.04
    options: --privileged
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:rw
```

## Building locally

```bash
docker build \
  --build-arg ANSIBLE_TAG=2.21-debian-trixie \
  -t ansible-test:2.21-debian-trixie \
  ansible-core/debian-trixie/
```

## Maintainer

* Written and maintained by [Will Hall](https://www.willhallonline.co.uk)

[hub]: https://hub.docker.com/r/willhallonline/ansible-test
