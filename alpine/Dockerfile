
FROM alpine:3.9

ENV TERRAFORM_VERSION=0.12.20

RUN set -eux; \
    sed -i 's,//[^/]*/,//mirrors.tuna.tsinghua.edu.cn/,g' /etc/apk/repositories && \
    apk update && \
    cat /etc/apk/repositories && \
    apk add sed zsh zsh-vcs wget curl git unzip  && \
    sed -i -E '/^root/s,/bin/.*sh,/usr/bin/zsh,g' /etc/passwd && \
    wget -O /tmp/install.sh https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh && \
    ZSH=~/.oh-my-zsh /bin/sh /tmp/install.sh  --unattended && \
    git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k && \
    git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions && \
    git clone https://github.com/zsh-users/zsh-completions ~/.oh-my-zsh/custom/plugins/zsh-completions && \
    cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc && \
    sed -i -E -e 's,^ZSH_THEME=.*$,ZSH_THEME="powerlevel9k/powerlevel9k"\nPOWERLEVEL9K_MODE="nerdfont-complete"\nPOWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(context dir vcs)\nPOWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(status time),g' -e 's,^plugins=\(,&\naws\nansible\nterraform\nzsh-autosuggestions\nzsh-completions\n,g' ~/.zshrc && \
    curl -fSL -o /tmp/terraform.zip "https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip" && \
    unzip /tmp/terraform.zip terraform -d /usr/local/bin/ && \
    apk add python2 py2-pip && \
    apk add --virtual .build-deps gcc musl-dev python2-dev libffi-dev openssl-dev && \
    pip install awscli && \
    pip install ansible && \
    rm -rf /tmp/* && \
    rm -rf ~/.cache/pip && \
    find /usr/lib \( -iname '*.pyc' -o -iname '*.pyo' \) -exec rm {} \; && \
    apk del .build-deps

CMD [ "zsh"]
