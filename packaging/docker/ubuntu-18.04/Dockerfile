FROM ubuntu:18.04

ARG TARGETOS
ARG TARGETARCH

ENV DEBIAN_FRONTEND=noninteractive
ENV DOCKER_COMPOSE_VERSION=1.27.4
ENV TINI_VERSION=0.19.0

RUN apt-get update && apt-get install -y --no-install-recommends \
    apt-transport-https \
    curl \
    ca-certificates \
    bash \
    git \
    gnupg-agent \
    jq \
    openssh-client \
    perl \
    python3 \
    python3-pip \
    rsync \
    software-properties-common \
    && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add - \
    && add-apt-repository \
    "deb [arch=$TARGETARCH] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
    && apt-get update \
    && apt-get install -y --no-install-recommends docker-ce-cli docker-compose-plugin \
    && rm -rf /var/lib/apt/lists/* \
    && pip3 install --upgrade pip setuptools \
    && pip3 install docker-compose==$DOCKER_COMPOSE_VERSION

RUN curl -Lfs -o /sbin/tini https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini-${TARGETARCH} && chmod +x /sbin/tini

ENV BUILDKITE_AGENT_CONFIG=/buildkite/buildkite-agent.cfg \
    PATH="/usr/local/bin:${PATH}"

RUN mkdir -p /buildkite/builds /buildkite/hooks /buildkite/plugins \
    && curl -Lfs -o /usr/local/bin/ssh-env-config.sh https://raw.githubusercontent.com/buildkite/docker-ssh-env-config/master/ssh-env-config.sh \
    && chmod +x /usr/local/bin/ssh-env-config.sh

COPY ./buildkite-agent.cfg /buildkite/buildkite-agent.cfg
COPY ./buildkite-agent-$TARGETOS-$TARGETARCH /usr/local/bin/buildkite-agent
COPY ./entrypoint.sh /usr/local/bin/buildkite-agent-entrypoint

VOLUME /buildkite
ENTRYPOINT ["buildkite-agent-entrypoint"]
CMD ["start"]
