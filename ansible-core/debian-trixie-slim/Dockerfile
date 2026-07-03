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

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        systemd \
        systemd-sysv \
        dbus && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    # Remove unnecessary systemd services
    rm -f /lib/systemd/system/multi-user.target.wants/* && \
    rm -f /etc/systemd/system/*.wants/* && \
    rm -f /lib/systemd/system/local-fs.target.wants/* && \
    rm -f /lib/systemd/system/sockets.target.wants/*udev* && \
    rm -f /lib/systemd/system/sockets.target.wants/*initctl* && \
    rm -f /lib/systemd/system/basic.target.wants/* && \
    rm -f /lib/systemd/system/anaconda.target.wants/*

VOLUME ["/sys/fs/cgroup", "/tmp", "/run"]
STOPSIGNAL SIGRTMIN+3
CMD ["/lib/systemd/systemd"]
