FROM alpine:3.4

MAINTAINER Kevin Wolff <kevin.wolff@weblemonade.com>

# Set version for s6 overlay
ARG OVERLAY_VERSION="v1.18.1.5"
ARG OVERLAY_ARCH="amd64"

# Environment variables
ENV PS1="$(whoami)@$(hostname):$(pwd)$ " \
    LANG="en_US.UTF-8" \
  	LC_ALL="en_US.UTF-8" \
  	LANGUAGE="en_US.UTF-8" \
  	TZ="Canada/Mountain" \
  	TERM="xterm-color"

RUN \
  # Update packages
  apk update && apk upgrade && \

  # Install base packages
  apk add --no-cache \
  	bash \
  	curl \
  	coreutils \
    ca-certificates \
  	tzdata \
    su-exec && \

  # Install shadow
  # Shadow provides pam-related utilities like useradd and usermod. Some processes such as postgres cannot run as root. See: https://pkgs.alpinelinux.org/package/edge/community/x86_64/shadow
  apk add --no-cache \
   	--repository http://nl.alpinelinux.org/alpine/edge/community \
   	shadow && \

  # Add s6 overlay
  # s6 is a collection of utilities revolving around process supervision and management, logging, and system initialization. See: http://skarnet.org/software/s6/overview.html
  curl -o \
    /tmp/s6-overlay.tar.gz -L \
    "https://github.com/just-containers/s6-overlay/releases/download/${OVERLAY_VERSION}/s6-overlay-${OVERLAY_ARCH}.tar.gz" && \
  tar xfz /tmp/s6-overlay.tar.gz -C / && \

  ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \

  # create container user
  groupmod -g 900 users && \
  adduser -D -s /bin/bash -u 1000 user && \
  usermod -G users user && \

  # cleanup
  rm -rf /tmp/*

# add init files
COPY root/ /

ENTRYPOINT ["/init"]
