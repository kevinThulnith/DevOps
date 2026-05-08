FROM ubuntu:22.04

# Tell apt-get to never interact
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update -qq && \
    apt-get install -y --no-install-recommends -qq \
    openssh-server \
    python3 \
    sudo \
    iproute2 \
    && \
    rm -rf /var/lib/apt/lists/* && \
    useradd -m -s /bin/bash ubuntu && \
    echo 'ubuntu:ubuntu' | chpasswd && \
    echo 'ubuntu ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers && \
    mkdir -p /run/sshd

EXPOSE 22

# Use exec form so SSH runs as PID 1
CMD ["/usr/sbin/sshd", "-D"]
