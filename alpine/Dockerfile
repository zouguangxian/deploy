
FROM alpine:3.9

ENV TERRAFORM_VERSION=0.12.20

RUN set -eux; \
    sed -i 's,//[^/]*/,//mirrors.tuna.tsinghua.edu.cn/,g' /etc/apk/repositories && \
    apk update && \
    cat /etc/apk/repositories && \
    apk add fish sed && \
    sed -i -E '/^root/s,/bin/.*sh,/usr/bin/fish,g' /etc/passwd && \
    apk add curl unzip && \
    curl -fSL -o /tmp/terraform.zip "https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip" && \
    unzip /tmp/terraform.zip terraform -d /usr/local/bin/ && \
    apk add python2 py2-pip && \
    apk add --virtual .build-deps gcc musl-dev python2-dev libffi-dev openssl-dev && \
    pip install awscli && \
    pip install ansible && \
    rm -rf /tmp/* && \
    rm -rf ~/.cache/pip && \
    find /usr/lib -iname '*.pyc' -o -iname '*.pyo' -exec rm {} \; && \
    apk del .build-deps

CMD [ "fish"]
