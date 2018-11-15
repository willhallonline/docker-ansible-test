FROM willhallonline/ansible:2.7-centos-7

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

RUN yum -y install systemd && \
    yum clean all

# Install Ansible inventory file and initctl_faker
COPY ./initctl_faker.sh /initctl
RUN chmod +x /initctl && rm -fr /sbin/initctl && ln -s /initctl /sbin/initctl

CMD [ "ansible-playbook", "--version" ]
