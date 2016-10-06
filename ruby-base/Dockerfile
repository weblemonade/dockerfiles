FROM ruby:2.3.1-alpine

MAINTAINER Kevin Wolff <kevin.wolff@weblemonade.com>

# ============================================
# Update, upgrade and install common packages
# ============================================

ENV BUILD_TOOLS='build-base libc-dev libxml2-dev libxslt-dev linux-headers '
ENV RAILS_DEPENDENCIES='nodejs sqlite-dev tzdata'
ENV UTILITIES='bash curl git iftop nano vim wget '

RUN apk update && apk upgrade && apk add --no-cache $UTILITIES $BUILD_TOOLS $RAILS_DEPENDENCIES

# ============================================
# Run the app
# ============================================
COPY harden.sh /usr/sbin/harden.sh
RUN /usr/sbin/harden.sh

CMD [ 'irb' ]