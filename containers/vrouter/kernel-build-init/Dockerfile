FROM ubuntu:16.04

ARG CONTAINER_NAME

# This image is used to build vrouter.ko for current ubuntu OS that it's running on.
# This container uses rpm packages to obtain source of vrouter kernel driver.
# The container needs /usr/src and /lib/modules mounted from the host to build driver.
# And we need to move out /usr/src all that packages can install there.

# this copy should be before yum install
COPY *.repo /etc/yum.repos.d/

RUN export DEBIAN_FRONTEND=noninteractive && \
    apt-get update && \
    apt-get install -y yum yum-utils rpm2cpio cpio dkms && \
    yum install -y contrail-vrouter-source && \
    mkdir -p /opt/contrail/src /contrail_tools && \
    mv /usr/src/* /opt/contrail/src/ && \
    rpm -q --qf "%{VERSION}" contrail-vrouter-source > /contrail_version && \
    cd /contrail_tools && \
    yumdownloader contrail-vrouter-utils && \
    rpm2cpio $(ls contrail-vrouter-utils*.rpm) | cpio -iuvd && \
    rm -f $(ls contrail-vrouter-utils*.rpm) && \
    cd .. && \
    yum clean all && \
    rm -rf /var/cache/yum && \
    apt-get purge -y yum yum-utils rpm2cpio cpio && \
    apt-get autoremove -y

COPY dkms.conf /opt/contrail/src/
COPY entrypoint.sh /

ENTRYPOINT ["/entrypoint.sh"]

LABEL net.juniper.contrail.container.name=$CONTAINER_NAME
