FROM willhallonline/ansible:2.10-ubuntu-20.04

# Labels.
LABEL maintainer="will@willhallonline.co.uk" \
    org.label-schema.schema-version="1.0" \
    org.label-schema.build-date=$BUILD_DATE \
    org.label-schema.vcs-ref=$VCS_REF \
    org.label-schema.name="willhallonline/ansible-test" \
    org.label-schema.description="Ansible testing inside Docker" \
    org.label-schema.url="https://github.com/willhallonline/docker-ansible-test" \
    org.label-schema.vcs-url="https://github.com/willhallonline/docker-ansible-test" \
    org.label-schema.vendor="Will Hall Online" \
    org.label-schema.docker.cmd="docker run --rm -it -v $(pwd):/ansible -v ~/.ssh/id_rsa:/root/id_rsa willhallonline/ansible-test:2.7-ubuntu-18.04"

RUN DEBIAN_FRONTEND=noninteractive && \
    apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y tzdata && \
    apt-get install -y systemd && \
    rm -rf /var/lib/apt/lists/* && \
    apt-get clean

# Install initctl_faker and systemctl script
COPY ./initctl_faker.sh /initctl
COPY ./systemctl.py /usr/bin/systemctl

RUN chmod +x /initctl && \
    rm -fr /sbin/initctl && \
    ln -s /initctl /sbin/initctl && \
    chmod +x /usr/bin/systemctl

VOLUME ["/sys/fs/cgroup", "/tmp", "/run"]
CMD ["/lib/systemd/systemd"]
