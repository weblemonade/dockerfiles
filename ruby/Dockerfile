FROM weblemonade/base:1.0

MAINTAINER Kevin Wolff <kevin.wolff@weblemonade.com>

# This images is based on the ruby:2.3.1-alpine Dockerfile
# It is almost twice the size due to the inclusion of build packages
# for building gems

# skip installing gem documentation
RUN mkdir -p /usr/local/etc \
	&& { \
		echo 'install: --no-document'; \
		echo 'update: --no-document'; \
	} >> /usr/local/etc/gemrc

ENV RUBY_MAJOR 2.3
ENV RUBY_VERSION 2.3.1
ENV RUBY_DOWNLOAD_SHA256 b87c738cb2032bf4920fef8e3864dc5cf8eae9d89d8d523ce0236945c5797dcd
ENV RUBYGEMS_VERSION 2.6.7

# some of ruby's build scripts are written in ruby
#   we purge system ruby later to make sure our final image uses what we just built
# readline-dev vs libedit-dev: https://bugs.ruby-lang.org/issues/11869 and https://github.com/docker-library/ruby/issues/75
RUN set -ex \
  # update and upgrade packages
  && apk update && apk upgrade \
  # packages required to build native gems and run rails
  && apk add --no-cache --virtual .rails-builddeps \
    build-base \
    libc-dev \
    libxml2-dev \
    libxslt-dev \
    linux-headers \
  # helpful utitlies for working within containers
  && apk add --no-cache --virtual .utilities \
    curl \
    wget \
    nano \
    vim \
    git \
  # packages required to compile ruby - these will be deleted
	&& apk add --no-cache --virtual .ruby-builddeps \
		autoconf \
		bison \
		bzip2 \
		bzip2-dev \
		ca-certificates \
		coreutils \
		gcc \
		gdbm-dev \
		glib-dev \
		libffi-dev \
		make \
		ncurses-dev \
		openssl \
		openssl-dev \
		procps \
		readline-dev \
		ruby \
		tar \
		yaml-dev \
		zlib-dev \
	\
	&& wget -O ruby.tar.gz "https://cache.ruby-lang.org/pub/ruby/$RUBY_MAJOR/ruby-$RUBY_VERSION.tar.gz" \
	&& echo "$RUBY_DOWNLOAD_SHA256 *ruby.tar.gz" | sha256sum -c - \
	\
	&& mkdir -p /usr/src/ruby \
	&& tar -xzf ruby.tar.gz -C /usr/src/ruby --strip-components=1 \
	&& rm ruby.tar.gz \
	\
	&& cd /usr/src/ruby \
	\
# hack in "ENABLE_PATH_CHECK" disabling to suppress:
#   warning: Insecure world writable dir
	&& { \
		echo '#define ENABLE_PATH_CHECK 0'; \
		echo; \
		cat file.c; \
	} > file.c.new \
	&& mv file.c.new file.c \
	\
	&& autoconf \
# the configure script does not detect isnan/isinf as macros
	&& ac_cv_func_isnan=yes ac_cv_func_isinf=yes \
		./configure --disable-install-doc \
	&& make -j"$(getconf _NPROCESSORS_ONLN)" \
	&& make install \
	\
	&& runDeps="$( \
		scanelf --needed --nobanner --recursive /usr/local \
			| awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
			| sort -u \
			| xargs -r apk info --installed \
			| sort -u \
	)" \
	&& apk add --virtual .ruby-rundeps $runDeps \
		bzip2 \
		ca-certificates \
		libffi-dev \
		openssl-dev \
		yaml-dev \
		procps \
		zlib-dev \
	&& apk del .ruby-builddeps \
	&& cd / \
	&& rm -r /usr/src/ruby

RUN gem update --system "$RUBYGEMS_VERSION"

ENV BUNDLER_VERSION 1.13.5

RUN gem install bundler --version "$BUNDLER_VERSION"

# install things globally, for great justice
# and don't create ".bundle" in all our apps
ENV GEM_HOME /usr/local/bundle
ENV BUNDLE_PATH="$GEM_HOME" \
	BUNDLE_BIN="$GEM_HOME/bin" \
	BUNDLE_SILENCE_ROOT_WARNING=1 \
	BUNDLE_APP_CONFIG="$GEM_HOME"
ENV PATH $BUNDLE_BIN:$PATH
RUN mkdir -p "$GEM_HOME" "$BUNDLE_BIN" \
	&& chmod 777 "$GEM_HOME" "$BUNDLE_BIN"

CMD [ 'irb' ]
