FROM ubuntu:18.04

RUN mkdir /openedx

RUN apt update && \
  apt upgrade -y && \
  apt install -y git wget autoconf bison build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm-dev

# Install ruby-build for building specific version of ruby
RUN git clone https://github.com/sstephenson/ruby-build.git /openedx/ruby-build
WORKDIR /openedx/ruby-build
RUN PREFIX=/usr/local ./install.sh

# Install ruby and some specific dependencies
ENV RUBY_VERSION 2.4.1
ENV BUNDLER_VERSION 1.11.2
ENV RAKE_VERSION 10.4.2
RUN ruby-build $RUBY_VERSION /openedx/ruby
ENV PATH "/openedx/ruby/bin:$PATH"
RUN gem install bundler -v $BUNDLER_VERSION
RUN gem install rake -v $RAKE_VERSION
# gem upgrade must come after bundler/rake install
RUN gem install rubygems-update && update_rubygems

# Install forum
RUN git clone https://github.com/edx/cs_comments_service.git --branch open-release/hawthorn.2 --depth 1 /openedx/cs_comments_service
WORKDIR /openedx/cs_comments_service
RUN bundle install --deployment

ENV RACK_ENV staging
ENV NEW_RELIC_ENABLE false
EXPOSE 4567
CMD ./bin/unicorn -c config/unicorn_tcp.rb -I '.'
