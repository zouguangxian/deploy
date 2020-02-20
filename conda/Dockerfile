FROM continuumio/miniconda:4.7.12-alpine

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
    find /usr/lib \( -iname '*.pyc' -o -iname '*.pyo' \) -exec rm {} \;

USER  10151

CMD [ "zsh", "--login", "-i" ]
