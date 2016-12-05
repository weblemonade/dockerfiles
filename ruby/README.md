# docker-ruby-base

## What is it and why does it exists?

This image [weblemonade/ruby](https://hub.docker.com/r/weblemonade/ruby/) extends the [weblmonade/base](https://hub.docker.com/r/weblemonade/base/) image which includes s6-overlay and builds ruby using the same install script as the ruby:2.3.1-alpine image.  It also includes some basic packages for debugging and building native gems for your rails projects.  

This image satisfies three objectives:

1.  Smallest possible base image for ruby/rails projects
2.  Avoid rebuilding dev packages for each project
3.  Standard tool set for debugging and maintenance

## Usage

Below is an example Dockerfile that extends weblemonade/ruby-base, adds postgres, imagemagick and installs Rails/gems from your Gemfile if it exists.


```bash

FROM weblemonade/ruby-base:latest

MAINTAINER Kevin Wolff <kevin.wolff@weblemonade.com>

# ============================================
# Keep packages up to date and add packages
# for building native gems
# ============================================

# need imagemagick and file for file uploads
ENV APP_PACKAGES="postgresql-dev imagemagick file"
RUN apk update && \
    apk upgrade && \
    apk add --no-cache $APP_PACKAGES


# ============================================
# Cache Gemfile for faster builds
# ============================================

WORKDIR /var/www/
ADD Gemfile /var/www/
ADD Gemfile.lock /var/www/


# ============================================
# Bundle install
# ============================================

RUN if [ -f Gemfile ]; then \
      bundle config --global build.nokogiri --use-system-libraries && \
      bundle install --without development test && \
      rm -rf /usr/lib/lib/ruby/gems/*/cache/*  && \
      rm -rf ~/.gem; \
    fi


# ============================================
# Bundle install
# ============================================

ADD . /var/www

# ============================================
# Save build date and version for reference
# ============================================

RUN date > build
RUN (git tag 2>/dev/null || echo) | tail -1 >> build

CMD [ "bundle", "exec", "puma", "-C", "config/puma.rb" ]
```
