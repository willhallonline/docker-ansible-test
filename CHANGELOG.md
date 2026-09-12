# Changelog

## Version Alignment

This project tracks the same versions as [docker-ansible](https://github.com/willhallonline/docker-ansible). See that project's [CHANGELOG](https://github.com/willhallonline/docker-ansible/blob/main/CHANGELOG.md) for detailed version history.

## ansible-test Specific Changes

### v2.7.3
- Align CI with the current `docker-ansible` build matrix and pinned Ansible
  versions.
- Use the QEMU version required for current Debian Trixie multi-architecture
  builds.
- Add image health checks for the active systemd variants.
- Keep Alpine 3.21–3.24 out of this project because Alpine uses OpenRC rather
  than systemd; Alpine 3.20 is deprecated upstream.

### v2.7.2
- fix: ensure root user for dockerfile build steps (#21)
- fix(ci): force root user in workflow smoke test (#19)
- docs: add SECURITY.md and link from README (#17)
- docs: prefer cgroup v2 over --privileged for systemd containers (#16)
- Build ubuntu-24.04 images for linux/amd64 only (#15)
- Add Dependabot configuration for Docker and GitHub Actions (#6)
- Various GitHub Actions dependency updates

### v6.4.1
- Inherit: Fix collection paths for Ubuntu 26.04 from docker-ansible #166
- Add ansible-test dependencies for sanity checks (#1)

### v6.4.0
- Inherit: Added Ansible 2.21.0 support from docker-ansible
- Added ansible-test dependencies (pycodestyle, pytest, yamllint, rstcheck, etc.) for full ansible-test sanity support
- GitHub Actions workflow for matrix builds (Debian, Ubuntu, Rocky Linux)
- Added systemd support across all test images

### v6.3.0 → v6.0.0
- Modernized from Ansible 2.9/2.10 (end-of-life) to Ansible 2.16–2.21
- Replaced GitLab CI with GitHub Actions
- Added multi-architecture support (amd64, arm64)
- Removed obsolete OS bases: CentOS 7/8, Ubuntu 18.04/20.04, Debian Stretch/Buster
- Added modern OS bases: Debian Bookworm/Trixie, Ubuntu 22.04/24.04/26.04, Rocky Linux 10
- Improved README with molecule integration examples and CI/CD guidance

### Previous Versions
Previous versions of ansible-test (supporting Ansible 2.9/2.10) are archived and no longer maintained. Use v6.0.0+ for current Ansible versions.
