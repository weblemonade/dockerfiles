FROM weblemonade/ruby:2.3.1

MAINTAINER Kevin Wolff <kevin.wolff@weblemonade.com>

ENV RAILS_VERSION 5.0.0.1

RUN apk update && apk upgrade && \
    apk add --no-cache \
      nodejs \
      file \
      imagemagick \
      postgresql-dev \
      mysql-dev \
      sqlite-dev

WORKDIR /var/www/

COPY Gemfile .
RUN bundle install

CMD ["irb"]
