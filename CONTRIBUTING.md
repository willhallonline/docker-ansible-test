# Contributing to Docker Ansible Test

Thank you for considering contributing to the project! I appreciate your time and effort and value your input. The aims of this file are to try to make it easier to contribute and I am happy to try and support contributions. Please bear in mind that the overall aims are to support testing of Ansible roles and playbooks across different OS versions and architectures.

## How Can I Contribute?

### Reporting Bugs

1. **Check Current Issues**: Before you create a new issue, please search for similar issues in the [Issue Tracker](https://github.com/willhallonline/docker-ansible-test/issues).
2. **Create a New Issue**: If your issue is new, please include:
   - **Title**: A succinct title that describes the issue.
   - **Description**: A detailed description, including steps to reproduce, and environment details (e.g., OS version, Docker version, Ansible version).
   - **Logs and Error Messages**: Any relevant logs or error messages from `ansible-test` or container operations.

### Suggesting Features

If you have an idea to improve the project, submit a feature request in the [Issue Tracker](https://github.com/willhallonline/docker-ansible-test/issues) and I'll try to respond.

### Submitting Pull Requests

1. **Fork the Repository**: Create a fork of the repository on GitHub.
2. **Clone Your Fork**: 

   ```bash
   git clone https://github.com/your-username/docker-ansible-test.git
   cd docker-ansible-test
   ```

3. **Create a Branch**:

   ```bash
   git checkout -b feature/your-feature-name
   ```

4. **Make Changes**: Develop your feature or fix in your local repository.
   - Changes to Dockerfiles should maintain consistency across all OS bases
   - Test locally: `docker build --build-arg ANSIBLE_TAG=2.22-debian-trixie -t test:local ansible-core/debian-trixie/`
   - Before changing the supported matrix, align the Dockerfiles, the matrix in
     `.github/workflows/build.yml`, the README support table, and this repository's
     changelog with the parent [docker-ansible](https://github.com/willhallonline/docker-ansible)
     matrix.

5. **Commit Changes**:

   ```bash
   git add .
   git commit -m "Description of your changes"
   ```

6. **Push Changes**:

   ```bash
   git push origin feature/your-feature-name
   ```

7. **Open a Pull Request**: Go to the repository on GitHub and open a pull request. Fill out the template provided, if available.

### Code Style and Testing

- **Follow Best Practices**: Ensure your code follows best practices for Dockerfiles.
- **Testing**: Thoroughly test your changes locally:
  - Build the image: `docker build --build-arg ANSIBLE_TAG=<version>-<os> -t test:local ansible-core/<os>/`
  - Run the CI smoke check:
    `docker run --rm --cgroupns=private --user root --tmpfs /run --tmpfs /run/lock -v /sys/fs/cgroup:/sys/fs/cgroup:rw test:local ansible-playbook --version`
  - Run collection sanity checks only from a collection project:
    `docker run --rm -v /path/to/collection:/opt/code test:local ansible-test sanity`
- **CI Scope**: Pull requests build the matrix as `linux/amd64` and run the
  smoke check above. Pushes and scheduled builds also publish supported images
  from repository-owned branches. The workflow is triggered by Dockerfile or
  workflow changes, not by documentation-only changes.
- **Documentation**: Update the README.md or CHANGELOG.md if your changes affect users.
- **Consistency**: Keep changes aligned with the parent [docker-ansible](https://github.com/willhallonline/docker-ansible) project structure.

### Reviewing and Approval

I make every effort to review pull requests promptly, but sometimes I may be busy or a request may be overlooked, so kindly ask for your patience during the process.

## Relationship to docker-ansible

This project extends [docker-ansible](https://github.com/willhallonline/docker-ansible) with systemd support for role and playbook testing. Changes that affect Ansible versions or base OS support should be coordinated with the parent project's releases.
