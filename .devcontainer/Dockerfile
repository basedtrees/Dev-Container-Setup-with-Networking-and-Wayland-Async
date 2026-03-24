FROM mcr.microsoft.com/devcontainers/base:ubuntu

RUN apt-get update && apt-get install -y \
    build-essential \
    libwayland-dev \
    wayland-protocols \
    rsync \
    inotify-tools \
    systemd \
    && rm -rf /var/lib/apt/lists/*

# Stage directory that the C binary rsyncs from (as required)
RUN mkdir -p /tmp/bootstrap && echo "rsync bootstrap marker" > /tmp/bootstrap/.ready
WORKDIR /workspace
COPY main.c .
