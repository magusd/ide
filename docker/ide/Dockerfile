FROM ubuntu:20.04

ARG NODE_VERSION=10.22.1
ARG NVM_DIR=/usr/local/nvm
ENV ANACONDA_DIR=/usr/local/miniconda
ENV PATH=$NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH
ENV THEIA_DEFAULT_PLUGINS=local-dir:plugins

RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections

RUN apt-get update -y && apt-get install -y build-essential wget curl gnupg gnupg-agent apt-transport-https ca-certificates python3-pip python curl g++ gcc \
  pkg-config lsb-release make nano git patch libbz2-dev bzip2 libgflags-dev libltdl-dev libsnappy-dev zlib1g-dev \
  vim screen tmux zsh software-properties-common nmap telnet net-tools jq pv zip unzip dnsutils libx11-dev libxkbfile-dev libsecret-1-dev

RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
  echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

## Install Node through NVM
RUN mkdir -p $NVM_DIR /root/.nvm
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash \
  && bash -c ". $NVM_DIR/nvm.sh && nvm install v$NODE_VERSION && nvm alias default v$NODE_VERSION && nvm use default"

RUN apt-get update \
    && pip3 install python-language-server flake8 autopep8 \
    && apt-get install -y yarn \
    && apt-get clean \
    && rm -rf /var/cache/apt/* \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /tmp/*

RUN mkdir -p /home/theia \
    && mkdir -p /home/project

WORKDIR /home/theia

ADD package.json package.json
RUN cat package.json
RUN yarn && rm -rf ./ycache \
    && yarn theia build \
    && yarn theia download:plugins

# RUN conda install -c conda-forge jupyterlab seaborn pyarrow pandas arrow sympy qgrid jupytext && \
#   pip3 install traces && \
#   jupyter nbextension enable --py --sys-prefix qgrid && \
#   jupyter nbextension enable --py --sys-prefix widgetsnbextension && \
#   jupyter serverextension enable jupytext && \
#   jupyter nbextension install --py jupytext && \
#   jupyter nbextension enable --py jupytext

# Install our code

EXPOSE 8888
EXPOSE 3000
EXPOSE 3500

# ADD deployment/docker/plugin-development/entrypoint.sh /carta/deployment/docker/plugin-development/entrypoint.sh
# ADD deployment/docker/plugin-development/supervisord.conf /carta/deployment/docker/plugin-development/supervisord.conf
# CMD ["entrypoint.sh"]
# CMD yarn theia start 