FROM alpine:3.8
LABEL maintainer="Damien JARRY <dev.damienjarry@gmail.com>" \
    version="6.2.3" \
    description="AngularCLI"

# Node ans Yarn versions - can be overloaded by --build-arg argument
ARG NODE_VERSION=10.9.0
ARG YARN_VERSION=1.10.1
ARG ANGULAR_VERSION=6.2.3
ENV NO_UPDATE_NOTIFIER=true

# Create user home
ENV USER visitor
ENV HOME /home/$USER
ENV PATH $PATH:$HOME
RUN mkdir -p $HOME
COPY dependencies/bashrc $HOME/.bashrc
VOLUME $HOME

# Install gosu to launch some commands as root
RUN apk add --no-cache --virtual build-deps curl gnupg dpkg \
    && for server in pgp.mit.edu keyserver.pgp.com ha.pool.sks-keyservers.net hkp://keyserver.ubuntu.com:80 hkp://p80.pool.sks-keyservers.net:80; do \
        gpg --keyserver $server --recv-keys \
        B42F6819007F00F88E364FD4036A9C25BF357DD4 && break; \
    done \
    && curl -o /usr/local/bin/gosu -SL "https://github.com/tianon/gosu/releases/download/1.10/gosu-$(dpkg --print-architecture | awk -F- '{ print $NF }')" \
    && curl -o /usr/local/bin/gosu.asc -SL "https://github.com/tianon/gosu/releases/download/1.10/gosu-$(dpkg --print-architecture | awk -F- '{ print $NF }').asc" \
    && gpg --verify /usr/local/bin/gosu.asc \
    && rm /usr/local/bin/gosu.asc \
    && chmod a+x /usr/local/bin/gosu \
    && chmod +s /usr/local/bin/gosu \
    && apk del build-deps \
    && rm -rf $HOME/.gnupg

# Install some useful things
RUN apk add --no-cache bash bash-completion build-base acl

# Install NodeJS
RUN \
    apk add --no-cache --virtual build-deps curl python linux-headers gnupg binutils-gold libstdc++ \
    && for server in pgp.mit.edu keyserver.pgp.com ha.pool.sks-keyservers.net hkp://keyserver.ubuntu.com:80 hkp://p80.pool.sks-keyservers.net:80; do \
        gpg --keyserver $server --recv-keys \
        94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
        FD3A5288F042B6850C66B31F09FE44734EB7990E \
        71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
        DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
        C4F0DFFF4E8C1A8236409D08E73BC641CC11F4C8 \
        B9AE9905FFD7803F25714661B63B535A4C206CA9 \
        56730D5401028683275BD23C23EFEFE93C4CFFFE && break; \
    done \
    && curl -o node-v${NODE_VERSION}.tar.xz -SL "https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}.tar.xz" \
    && curl -o SHASUMS256.txt.asc -SL "https://nodejs.org/dist/v${NODE_VERSION}/SHASUMS256.txt.asc" \
    && gpg --verify SHASUMS256.txt.asc \
    && rm SHASUMS256.txt.asc \
    && tar -xf node-v${NODE_VERSION}.tar.xz \
    && rm -rf node-v${NODE_VERSION}.tar.xz \
    && cd node-v${NODE_VERSION} \
    && ./configure --prefix=/usr --without-npm \
    && make -j$(getconf _NPROCESSORS_ONLN) \
    && make install \
    && cd - \
    && rm -rf node-v${NODE_VERSION} \
    && apk del build-deps \
    && rm -rf /usr/share/man $HOME/.gnupg

# Install YARN
RUN if [ "$YARN_VERSION" != "no" ]; then \
        apk add --no-cache --virtual build-deps curl gnupg \
        && cd /tmp \
        && for server in pgp.mit.edu keyserver.pgp.com ha.pool.sks-keyservers.net hkp://keyserver.ubuntu.com:80 hkp://p80.pool.sks-keyservers.net:80; do \
            gpg --keyserver $server --recv-keys \
            6A010C5166006599AA17F08146C2130DFD2497F5 && break; \
        done \
        && curl -sfSL -O https://github.com/yarnpkg/yarn/releases/download/v${YARN_VERSION}/yarn-v${YARN_VERSION}.tar.gz -O https://github.com/yarnpkg/yarn/releases/download/v${YARN_VERSION}/yarn-v${YARN_VERSION}.tar.gz.asc \
        && gpg --batch --verify yarn-v${YARN_VERSION}.tar.gz.asc yarn-v${YARN_VERSION}.tar.gz \
        && mkdir /usr/local/share/yarn \
        && tar -xf yarn-v${YARN_VERSION}.tar.gz -C /usr/local/share/yarn --strip 1 \
        && ln -s /usr/local/share/yarn/bin/yarn /usr/local/bin/ \
        && ln -s /usr/local/share/yarn/bin/yarnpkg /usr/local/bin/ \
        && rm yarn-v${YARN_VERSION}.tar.gz* \
        && apk del build-deps \
        && rm -rf /usr/share/man $HOME/.gnupg; \
    fi

# Install AngularCLI
RUN yarn global add @angular/cli@${ANGULAR_VERSION} \
    && ng set --global packageManager=yarn

# Add entrypoint
COPY dependencies/entrypoint /usr/local/bin/entrypoint
ENTRYPOINT ["/usr/local/bin/entrypoint"]

# Define workdir and set it as volume
ENV WORKDIR /data
WORKDIR $WORKDIR
VOLUME $WORKDIR

# "bash" at default command
# Beware that /bin/sh is busybox and not bash
CMD ["bash"]
