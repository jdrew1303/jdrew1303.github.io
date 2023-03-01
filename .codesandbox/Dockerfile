FROM ubuntu

RUN apt-get update && apt-get -y install make build-essential ruby ruby-dev

ENV GEM_HOME=$HOME/gems
ENV PATH=$HOME/gems/bin:$PATH

RUN gem install jekyll bundler