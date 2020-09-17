FROM centos:8

LABEL maintainer="dev@crontis.net"

# install python3 with pip, this is needed for the aws api
RUN dnf install -y python3 python3-pip
# install dnf-plugin-core to enable "dnf config-manager" which we need to add the packer repo
# install epel-release to install ansible
RUN dnf install -y dnf-plugins-core epel-release
# add packer repo
RUN dnf config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
# install ansible and packer
RUN dnf install -y ansible packer
# install awscli and boto. boto is an python lib to communicate for aws
RUN pip3 install awscli boto boto3 botocore
# install aws session manager plugin, this is used by packer to connect to our ec2 instance
RUN rpm -i https://s3.amazonaws.com/session-manager-downloads/plugin/latest/linux_64bit/session-manager-plugin.rpm

# install ssh client
RUN dnf install -y openssh-clients

# Clear dnf cache to reduce image size
RUN dnf clean all

# Create a dir to work in
RUN mkdir /build

# Add non root user to prevent issues with ansible, ssh and session manager later on
RUN useradd -m centos
USER centos

# Switch to working dir
WORKDIR /build

# set entrypoint to bash because this image supports multiple tools
ENTRYPOINT [ "/bin/bash", "-c" ]