FROM alpine:3.4

MAINTAINER Kevin Wolff <kevin.wolff@weblemonade.com>

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

  ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \

  # cleanup
  rm -rf /tmp/*

CMD ["/bin/bash"]
