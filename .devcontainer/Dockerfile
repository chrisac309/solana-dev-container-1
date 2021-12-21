FROM ubuntu:18.04

WORKDIR /home/

COPY . .

## update and install some things we should probably have
RUN apt-get update
RUN apt-get install -y \
  curl \
  git \
  gnupg2 \
  jq \
  sudo \
  zsh \
  vim \
  build-essential \
  openssl \
  libssl-dev \
  pkg-config \
  libudev-dev

## Install rustup and common components
RUN curl https://sh.rustup.rs -sSf | sh -s -- -y --default-toolchain stable
ENV PATH /root/.cargo/bin:$PATH

RUN rustup install nightly
RUN rustup component add rustfmt
RUN rustup component add rustfmt --toolchain nightly
RUN rustup component add clippy
RUN rustup component add clippy --toolchain nightly

RUN cargo install cargo-expand
RUN cargo install cargo-edit

# install solana
RUN sh -c "$(curl -sSfL https://release.solana.com/v1.8.2/install)"
ENV PATH="/root/.local/share/solana/install/active_release/bin:$PATH"

RUN echo 'PATH=$PATH:/$PATH:/opt/solana-1.8.2/bin' >> ~/.bashrc

RUN RUST_BACKTRACE=full cargo install --git https://github.com/project-serum/anchor anchor-cli --locked

# replace shell with bash so we can source files
RUN rm /bin/sh && ln -s /bin/bash /bin/sh


# nvm environment variables
ENV NVM_DIR /usr/local/nvm
ENV NODE_VERSION 14.18.1

# install nvm
# https://github.com/creationix/nvm#install-script
RUN curl --silent -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.2/install.sh | bash

# install node, npm and yarn using nvm
RUN source $NVM_DIR/nvm.sh \
    && nvm install $NODE_VERSION \
    && nvm alias default $NODE_VERSION \
    && nvm use default \
    && npm install -g yarn


# add node and npm to path so the commands are available
ENV NODE_PATH $NVM_DIR/v$NODE_VERSION/lib/node_modules
ENV PATH $NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH

# confirm installation
RUN node -v
RUN npm -v
