FROM ubuntu:22.04

# Non‑interactive, no prompts
ENV DEBIAN_FRONTEND=noninteractive

# Pre‑configure the timezone so tzdata doesn't ask
RUN ln -fs /usr/share/zoneinfo/Etc/UTC /etc/localtime && \
    apt-get update -qq && \
    apt-get install -y --no-install-recommends -qq \
    ansible \
    openssh-client \
    python3 \
    python3-pip \
    sshpass \
    tzdata \
    && \
    pip install docker docker-compose -q && \
    rm -rf /var/lib/apt/lists/*

CMD ["tail", "-f", "/dev/null"]
