# Ansible Testing

Docker images with **systemd** enabled for testing Ansible roles and playbooks inside containers. These images extend [willhallonline/ansible](https://github.com/willhallonline/docker-ansible) with systemd support, making them ideal for CI/CD testing of roles that manage services or interact with the init system.

**Version Alignment:** ansible-test versions track [docker-ansible](https://github.com/willhallonline/docker-ansible) releases. Both projects support the same Ansible Core versions (2.16–2.21) and base OS combinations.

[![Docker Pulls](https://img.shields.io/docker/pulls/willhallonline/ansible-test "Docker Pulls")][hub] ![Docker Image Size (tag)](https://img.shields.io/docker/image-size/willhallonline/ansible-test/latest)

## Current Ansible Core Versions

These are the latest Ansible Core versions running within the containers. **Versions match [docker-ansible](https://github.com/willhallonline/docker-ansible):**

- Ansible 2.16: 2.16.14
- Ansible 2.17: 2.17.14
- Ansible 2.18: 2.18.9
- Ansible 2.19: 2.19.2
- Ansible 2.20: 2.20.0
- Ansible 2.21: 2.21.0

See [CHANGELOG.md](CHANGELOG.md) for version history and [docker-ansible CHANGELOG](https://github.com/willhallonline/docker-ansible/blob/main/CHANGELOG.md) for detailed release notes.

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

These containers run systemd as PID 1 and need a delegated cgroup hierarchy to function. You do **not** need `--privileged` on a modern host — see the [Security note](#security-note-avoid---privileged) below for why that flag should be a last resort.

### Interactive shell with systemd

```bash
docker run --rm -it \
  --cgroupns=private \
  --tmpfs /run --tmpfs /run/lock \
  -v /sys/fs/cgroup:/sys/fs/cgroup:rw \
  willhallonline/ansible-test:latest /bin/bash
```

### Testing an Ansible role (with systemd support)

```bash
docker run --rm \
  --cgroupns=private \
  --tmpfs /run --tmpfs /run/lock \
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
    options: --cgroupns=private
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
    cgroupns_mode: private
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:rw
    tmpfs:
      - /run
      - /run/lock
    docker_networks:
      - name: ansible_test
```

### Bash Alias

You can put these inside your dotfiles (~/.bashrc or ~/.zshrc).

```bash
alias docker-ansible-test='docker run --rm -it --cgroupns=private --tmpfs /run --tmpfs /run/lock -v /sys/fs/cgroup:/sys/fs/cgroup:rw -v $(pwd):/ansible willhallonline/ansible-test:latest /bin/bash'
alias docker-ansible-test-play='docker run --rm --cgroupns=private --tmpfs /run --tmpfs /run/lock -v /sys/fs/cgroup:/sys/fs/cgroup:rw -v $(pwd):/ansible willhallonline/ansible-test:latest ansible-playbook'
```

### Security note: avoid `--privileged`

`--privileged` grants the container **all** Linux capabilities, disables seccomp/AppArmor confinement, and exposes every host device node. A container running like this is effectively root on the host — a malicious or buggy Ansible role under test could mount the host disk, load kernel modules, or escape via the classic cgroup `release_agent` trick. Since this image is designed to run arbitrary, possibly untrusted, playbooks and roles, that's a real risk, not a theoretical one.

Prefer, in order:

1. **cgroup v2 + `--cgroupns=private`** (shown above) — works on any modern host (Docker 20.10+, cgroup v2 default on current Debian/Ubuntu/Fedora/RHEL/Rocky). No extra capabilities or relaxed seccomp needed; systemd is designed to run this way in containers.
2. **Rootless Podman** (`podman run --systemd=always ...`) — Podman configures the systemd environment automatically and maps container root to an unprivileged host user.
3. **cgroup v1 fallback** (older hosts only), granting just the capability systemd actually needs instead of everything:
   ```bash
   docker run --rm \
     --cap-add SYS_ADMIN \
     --security-opt seccomp=unconfined \
     -v /sys/fs/cgroup:/sys/fs/cgroup:ro \
     --tmpfs /run --tmpfs /run/lock \
     willhallonline/ansible-test:latest
   ```
4. **`--privileged`** — only if none of the above work for your environment (e.g. an old CI runner you don't control). Treat any container run this way as equivalent to host root, and never use it to run untrusted playbooks.

For a fuller discussion of these risks and hardening options, see [SECURITY.md](SECURITY.md).

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

- Containers need a delegated cgroup hierarchy (`--cgroupns=private` plus `-v /sys/fs/cgroup:/sys/fs/cgroup:rw`) — `--privileged` is not required on modern hosts; see the [Security note](#security-note-avoid---privileged) above
- The container starts systemd as PID 1, allowing `service` and `systemctl` commands to work
- Useful for testing roles that:
  - Enable/disable/start/stop services
  - Use `systemd` module
  - Interact with systemd timers or targets

### Running Services

When the container starts (without overriding CMD), systemd will start and manage services:

```bash
docker run --rm \
  --cgroupns=private \
  --tmpfs /run --tmpfs /run/lock \
  -v /sys/fs/cgroup:/sys/fs/cgroup:rw \
  willhallonline/ansible-test:2.21-debian-trixie
```

You can then use `docker exec` to run ansible commands against the running container:

```bash
docker exec <container-id> ansible-playbook /ansible/playbook.yml
```

## Maintainer

- Written and maintained by [Will Hall](https://www.willhallonline.co.uk)
- Part of the [docker-ansible](https://github.com/willhallonline/docker-ansible) project family

For contributing guidelines, see [CONTRIBUTING.md](CONTRIBUTING.md).

For a discussion of the security trade-offs of running systemd-in-containers with this image (including `--privileged` risks and how to report vulnerabilities), see [SECURITY.md](SECURITY.md).

[hub]: https://hub.docker.com/r/willhallonline/ansible-test
