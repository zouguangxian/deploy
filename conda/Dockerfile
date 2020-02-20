# copy from https://github.com/ContinuumIO/docker-images/blob/master/miniconda3/alpine/Dockerfile
FROM alpine:3.9 as alpine-glibc

#  $ docker build . -t continuumio/miniconda3:4.7.10-alpine
#  $ docker push continuumio/miniconda3:4.7.10-alpine

LABEL MAINTAINER="Vlad Frolov"
LABEL SRC=https://github.com/frol/docker-alpine-glibc
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /opt/conda/bin:$PATH

RUN ALPINE_GLIBC_BASE_URL="https://github.com/sgerrand/alpine-pkg-glibc/releases/download" && \
    ALPINE_GLIBC_PACKAGE_VERSION="2.28-r0" && \
    ALPINE_GLIBC_BASE_PACKAGE_FILENAME="glibc-$ALPINE_GLIBC_PACKAGE_VERSION.apk" && \
    ALPINE_GLIBC_BIN_PACKAGE_FILENAME="glibc-bin-$ALPINE_GLIBC_PACKAGE_VERSION.apk" && \
    ALPINE_GLIBC_I18N_PACKAGE_FILENAME="glibc-i18n-$ALPINE_GLIBC_PACKAGE_VERSION.apk" && \
    apk add --no-cache --virtual=.build-dependencies wget ca-certificates && \
    echo \
        "-----BEGIN PUBLIC KEY-----\
        MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEApZ2u1KJKUu/fW4A25y9m\
        y70AGEa/J3Wi5ibNVGNn1gT1r0VfgeWd0pUybS4UmcHdiNzxJPgoWQhV2SSW1JYu\
        tOqKZF5QSN6X937PTUpNBjUvLtTQ1ve1fp39uf/lEXPpFpOPL88LKnDBgbh7wkCp\
        m2KzLVGChf83MS0ShL6G9EQIAUxLm99VpgRjwqTQ/KfzGtpke1wqws4au0Ab4qPY\
        KXvMLSPLUp7cfulWvhmZSegr5AdhNw5KNizPqCJT8ZrGvgHypXyiFvvAH5YRtSsc\
        Zvo9GI2e2MaZyo9/lvb+LbLEJZKEQckqRj4P26gmASrZEPStwc+yqy1ShHLA0j6m\
        1QIDAQAB\
        -----END PUBLIC KEY-----" | sed 's/   */\n/g' > "/etc/apk/keys/sgerrand.rsa.pub" && \
    wget \
        "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_BASE_URL/$ALPINE_GLIBC_PACKAGE_VERSION/$ALPINE_GLIBC_I18N_PACKAGE_FILENAME" && \
    apk add --no-cache \
        "$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_I18N_PACKAGE_FILENAME" && \
    \
    rm "/etc/apk/keys/sgerrand.rsa.pub" && \
    /usr/glibc-compat/bin/localedef --force --inputfile POSIX --charmap UTF-8 "$LANG" || true && \
    echo "export LANG=$LANG" > /etc/profile.d/locale.sh && \
    \
    apk del glibc-i18n && \
    \
    rm "/root/.wget-hsts" && \
    apk del .build-dependencies && \
    rm \
        "$ALPINE_GLIBC_BASE_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_BIN_PACKAGE_FILENAME" \
        "$ALPINE_GLIBC_I18N_PACKAGE_FILENAME"

FROM alpine-glibc as miniconda

LABEL MAINTAINER="Anaconda, Inc"
ENV CONDA_VERSION 4.7.12
ENV CONDA_MD5 0dba759b8ecfc8948f626fa18785e3d8

# Create non-root user, install dependencies, install Conda
RUN addgroup -S anaconda && \
    adduser -D -u 10151 anaconda -G anaconda && \
    apk add --no-cache --virtual=.build-dependencies wget ca-certificates && \
    wget --quiet https://repo.continuum.io/miniconda/Miniconda3-$CONDA_VERSION-Linux-x86_64.sh && \
    echo "${CONDA_MD5}  Miniconda3-$CONDA_VERSION-Linux-x86_64.sh" > miniconda.md5 && \
    if [ $(md5sum -c miniconda.md5 | awk '{print $2}') != "OK" ] ; then exit 1; fi && \
    mv Miniconda3-$CONDA_VERSION-Linux-x86_64.sh miniconda.sh && \
    mkdir -p /opt && \
    sh ./miniconda.sh -b -p /opt/conda && \
    rm miniconda.sh miniconda.md5 && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    chown -R anaconda:anaconda /opt && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> /home/anaconda/.profile && \
    echo "conda activate base" >> /home/anaconda/.profile && \
    find /opt/conda/ \( -iname '*.pyc' -o -iname '*.pyo' \) -delete && \
    apk del .build-dependencies && \
    find /opt/conda/ -follow -type f -name '*.a' -delete && \
    find /opt/conda/ -follow -type f -name '*.js.map' -delete && \
    /opt/conda/bin/conda clean -afy


USER  10151
ENV PATH "/bin:/sbin:/usr/bin"

CMD [ "sh", "--login", "-i" ]

FROM miniconda

USER root

RUN set -eux; \
    sed -i 's,//[^/]*/,//mirrors.tuna.tsinghua.edu.cn/,g' /etc/apk/repositories && \
    apk update && \
    apk add zsh zsh-vcs sed wget curl git && \
    wget -O /tmp/install.sh https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh && \
    ZSH=/home/anaconda/.oh-my-zsh /bin/sh /tmp/install.sh  --unattended && \
    git clone https://github.com/bhilburn/powerlevel9k.git /home/anaconda/.oh-my-zsh/custom/themes/powerlevel9k && \
    git clone https://github.com/zsh-users/zsh-autosuggestions /home/anaconda/.oh-my-zsh/custom/plugins/zsh-autosuggestions && \
    git clone https://github.com/zsh-users/zsh-completions /home/anaconda/.oh-my-zsh/custom/plugins/zsh-completions && \
    cp /home/anaconda/.oh-my-zsh/templates/zshrc.zsh-template /home/anaconda/.zshrc && \
    sed -i -E -e 's,^ZSH_THEME=.*$,ZSH_THEME="powerlevel9k/powerlevel9k"\nPOWERLEVEL9K_MODE="nerdfont-complete"\nPOWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(context dir vcs)\nPOWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(status time),g' -e 's,^plugins=\(,&\nzsh-autosuggestions\nzsh-completions\n,g' /home/anaconda/.zshrc && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> /home/anaconda/.zshrc && \
    echo "conda activate base" >> /home/anaconda/.zshrc && \
    chown -R anaconda:anaconda /home/anaconda && \
    /opt/conda/bin/conda install --yes -c conda-forge curl awscli terraform ansible && \
    /opt/conda/bin/conda clean -afy && \
    find /opt/conda/ \( -iname '*.pyc' -o -iname '*.pyo' \) -delete

USER  10151

CMD [ "zsh", "--login", "-i" ]
