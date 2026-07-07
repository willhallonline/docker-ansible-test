# Security Policy

## Overview

The images in this project run **systemd as PID 1** inside a container so that Ansible roles which manage services, timers, and other init-system state can be tested realistically. Making systemd work this way requires relaxing some of the isolation Docker normally provides, which has real security implications. This document explains those trade-offs and how to reduce risk.

## Key risks

### `--privileged` is effectively host root

Older guidance (and some tutorials elsewhere) suggests running these containers with `--privileged`. That flag:

- Grants **all** Linux capabilities (`CAP_SYS_ADMIN`, `CAP_SYS_MODULE`, `CAP_SYS_PTRACE`, etc.)
- Disables seccomp and AppArmor/SELinux confinement (`unconfined`)
- Exposes every host device node (`/dev/sda`, `/dev/mem`, ...) to the container

A process with those permissions is not meaningfully contained. Well-known, low-effort escape techniques from a `--privileged` container include:

- Mounting a host block device and `chroot`-ing into the host filesystem
- Loading an arbitrary kernel module via `CAP_SYS_MODULE`
- Abusing the cgroup v1 `release_agent`/`notify_on_release` mechanism to execute a payload as root in the host namespace

**Do not run `--privileged` containers with untrusted or unreviewed Ansible roles/playbooks**, and avoid it in any CI environment where the runner itself matters (self-hosted runners, shared build machines). A compromised or malicious role under test can pivot straight to host compromise.

### Running arbitrary/third-party Ansible content

These images are a *test harness* — they are designed to execute whatever playbook or role you point at them, including third-party roles from Ansible Galaxy or a public PR. Treat any role/playbook you haven't audited as untrusted input, the same way you would treat an unreviewed shell script. Combined with elevated container privileges, running unreviewed roles is the highest-risk scenario this project can be used in.

### `--cgroup-fs` / cgroup mounts as an attack surface

Mounting `/sys/fs/cgroup` read-write into the container (required for systemd to manage services) gives the container write access to a cgroup hierarchy. On a properly delegated cgroup v2 subtree (see below) this is safe and is the standard way systemd-in-containers is expected to run. On cgroup v1, a shared/non-delegated mount can allow a container to affect sibling containers or, in the `release_agent` case above, the host.

## Recommended configuration (lowest risk → highest risk)

1. **cgroup v2 + `--cgroupns=private`** (recommended default — see [README.md](README.md#running)). No extra capabilities or relaxed seccomp are required; systemd is designed to run this way in a delegated cgroup namespace on any host with cgroup v2 (the default on current Debian/Ubuntu/Fedora/Rocky/RHEL and Docker 20.10+).
2. **Rootless Podman** (`podman run --systemd=always ...`). Podman configures the systemd environment automatically and maps container root to an unprivileged host user, so even a successful container breakout does not yield host root.
3. **cgroup v1 fallback**, granting only the capability systemd actually needs (`--cap-add SYS_ADMIN`) instead of the full set, with seccomp relaxed only as far as necessary. Use this only on hosts that don't support cgroup v2.
4. **`--privileged`** — last resort only, for environments where none of the above work (e.g. an old CI runner you don't control). Never use this option together with untrusted playbooks/roles, and never on infrastructure you can't afford to have compromised.

See [README.md](README.md#security-note-avoid---privileged) for the exact `docker run` invocations for each option.

## Additional hardening

- **`--userns-remap`** (Docker) or rootless Podman: maps container UID 0 to an unprivileged host UID, so a breakout lands as a non-root host user even if other mitigations fail. Recommended as defense-in-depth regardless of which option above you choose.
- **Ephemeral, disposable runs**: use `--rm` (as shown throughout the README) so containers don't persist state between test runs, and avoid mounting sensitive host paths beyond what's needed (e.g. don't bind-mount your home directory or SSH keys into the container).
- **Isolate CI runners**: if you run this image with elevated privileges in CI, do so on ephemeral/disposable runners (e.g. GitHub-hosted runners, or self-hosted runners dedicated to this purpose) rather than shared, long-lived infrastructure.
- **Keep images up to date**: pull the latest tag before testing so you have current systemd, dbus, and OS package security fixes.

## Reporting a vulnerability

If you discover a security issue in the Dockerfiles, build process, or CI configuration of this project (as opposed to the general risks of running systemd-in-containers described above), please report it privately rather than opening a public issue:

- Open a [GitHub Security Advisory](https://github.com/willhallonline/docker-ansible-test/security/advisories/new) for this repository, or
- Email the maintainer directly at will@willhallonline.co.uk

Please include steps to reproduce and, if possible, the affected image tag(s). We'll acknowledge reports as soon as we can and aim to publish a fix or mitigation promptly.
