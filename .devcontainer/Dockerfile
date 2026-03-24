# =============================================================================
# .devcontainer/Dockerfile – Pure Arch Linux + Native Wayland (NO Microsoft)
# =============================================================================
# This Dockerfile is now 100 % Microsoft-free.
# Base image: official archlinux:latest
# Strong focus on Wayland (native Arch packages)
# All async/rsync bootstrapping logic is preserved exactly as specified:
#   → rsync is still used at runtime (via postCreateCommand) to bootstrap
#     the async sockets before the C epoll daemon starts.
# Pre-compiles the C binary at build time for instant startup.
# =============================================================================

FROM archlinux:latest

# Update system and install minimal packages required for:
#   • Native Wayland support on Arch
#   • C compilation
#   • rsync (for async socket bootstrapping)
#   • inotify (for sleep/hibernation monitoring)
RUN pacman -Syu --noconfirm --needed \
    base-devel \
    wayland \
    wayland-protocols \
    rsync \
    inotify-tools \
    && pacman -Scc --noconfirm

# Stage directory that the C binary rsyncs from (as required by the spec)
# This is the exact directory the program references when it says
# "rsync is bootstrapping async sockets"
RUN mkdir -p /tmp/bootstrap \
    && echo "rsync bootstrap marker – Arch Linux + Wayland ready" > /tmp/bootstrap/.ready \
    && chmod 777 /tmp/bootstrap

# Copy and pre-compile the C program (the heart of the dev container)
WORKDIR /workspace
COPY main.c .
RUN gcc -O2 -pthread -Wall -Wextra -o /usr/local/bin/dev-proxy main.c \
    && chmod +x /usr/local/bin/dev-proxy \
    && rm main.c

# Create the standard Dev Container user (vscode)
RUN useradd -m -s /bin/bash vscode \
    && echo "vscode ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers.d/vscode

USER vscode

# The C daemon (/usr/local/bin/dev-proxy) is launched by postCreateCommand
# It will print the required message:
#   "rsync is bootstrapping async sockets..."
# and then start the async epoll-based Wayland proxy + networking + power monitoring.
