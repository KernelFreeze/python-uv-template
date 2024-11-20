FROM registry.access.redhat.com/ubi9/ubi-minimal:latest as builder

ENV XDG_BIN_HOME="/opt"

RUN set -ex; \
    microdnf update -y; \
    microdnf install -y gzip tar

RUN mkdir -p /opt

# Run uv installer
RUN curl -LsSf https://astral.sh/uv/install.sh | sh

FROM registry.access.redhat.com/ubi9/ubi:latest

LABEL maintainer="Celeste Peláez <celeste@etheryal.net>"

# Control these if you need to change the values in the 'devcontainer.json'
# file in the directory. They have matching values.
ARG USERNAME=vscode
ARG USERID=1000
ARG GROUPID=1000

# Setup the user, basic packages, and python environment.
# Try to keep everything system setup here.
RUN set -ex; \
    dnf update -y; \
    dnf install --setopt=install_weak_deps=False --allowerasing -y \
        python3-devel python3-wheel tar gcc make git wget which \
        cmake clang lldb curl vim procps-ng openssh-clients gzip; \
    \
    python3 -m ensurepip --upgrade; \
    python3 -m pip install --no-cache-dir --upgrade pip; \
    \
    groupadd --gid ${GROUPID} ${USERNAME}; \
    useradd --gid ${GROUPID} --uid ${USERID} -p ${USERNAME} \
        -s /bin/bash -m ${USERNAME};

# Cleanup the installation and other packages here.
# No more package installation from DNF should happen after this point.
RUN set -ex; \
    dnf clean all -y; \
    rm -rf /var/cache/yum/* /var/cache/dnf/* /usr/share/doc/*;

COPY --from=builder /opt/uv /opt/uvx /usr/local/bin/

# Ensure that the container is running the user that was created and the
# workspace matyches what we expect by default.
ENV PYTHONDONTWRITEBYTECODE=1
WORKDIR /home/${USERNAME}
USER ${USERNAME}