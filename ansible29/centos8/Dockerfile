FROM willhallonline/ansible:2.9-centos-8

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
    org.label-schema.docker.cmd="docker run --rm -it -v $(pwd):/ansible -v ~/.ssh/id_rsa:/root/id_rsa willhallonline/ansible-test:2.7-centos"

# Install systemd -- See https://hub.docker.com/_/centos/
RUN dnf -y update; dnf clean all; \
    (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
    rm -f /lib/systemd/system/multi-user.target.wants/*;\
    rm -f /etc/systemd/system/*.wants/*;\
    rm -f /lib/systemd/system/local-fs.target.wants/*; \
    rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
    rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
    rm -f /lib/systemd/system/basic.target.wants/*;\
    rm -f /lib/systemd/system/anaconda.target.wants/*;

# Install requirements.
RUN dnf -y update && \
    dnf -y install epel-release initscripts && \
    dnf -y update && \
    dnf -y install \
        sudo \
        which  && \
    dnf clean all

# Install initctl_faker and systemctl script
COPY ./initctl_faker.sh /initctl
COPY ./systemctl.py /usr/bin/systemctl

RUN chmod +x /initctl && \
    rm -fr /sbin/initctl && \
    ln -s /initctl /sbin/initctl && \
    chmod +x /usr/bin/systemctl

CMD [ "ansible-playbook", "--version" ]
