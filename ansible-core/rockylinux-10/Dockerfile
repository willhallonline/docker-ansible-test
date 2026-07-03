ARG ANSIBLE_TAG
FROM willhallonline/ansible:${ANSIBLE_TAG}

# Labels.
LABEL maintainer="will@willhallonline.co.uk" \
    org.label-schema.schema-version="1.0" \
    org.label-schema.name="willhallonline/ansible-test" \
    org.label-schema.description="Ansible testing inside Docker" \
    org.label-schema.url="https://github.com/willhallonline/docker-ansible-test" \
    org.label-schema.vcs-url="https://github.com/willhallonline/docker-ansible-test" \
    org.label-schema.vendor="Will Hall Online"

RUN dnf -y install systemd systemd-libs dbus && \
    dnf clean all && \
    # Remove unnecessary systemd services
    (cd /lib/systemd/system/sysinit.target.wants/ && \
        for i in *; do [ "$i" = "systemd-tmpfiles-setup.service" ] || rm -f "$i"; done) && \
    rm -f /lib/systemd/system/multi-user.target.wants/* && \
    rm -f /etc/systemd/system/*.wants/* && \
    rm -f /lib/systemd/system/local-fs.target.wants/* && \
    rm -f /lib/systemd/system/sockets.target.wants/*udev* && \
    rm -f /lib/systemd/system/sockets.target.wants/*initctl* && \
    rm -f /lib/systemd/system/basic.target.wants/* && \
    rm -f /lib/systemd/system/anaconda.target.wants/*

# Install Python packages required for ansible-test sanity checks.
RUN pip3 install --no-cache-dir \
        pycodestyle \
        pytest \
        pytest-mock \
        yamllint \
        rstcheck \
        pathspec \
        voluptuous

VOLUME ["/sys/fs/cgroup", "/tmp", "/run"]
STOPSIGNAL SIGRTMIN+3
CMD ["/lib/systemd/systemd"]
