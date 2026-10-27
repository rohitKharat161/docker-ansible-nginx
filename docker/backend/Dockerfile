FROM nginx:alpine

RUN apk add --no-cache openssh-server python3 sudo \
    && adduser -D -s /bin/sh ansible \
    && echo 'ansible:TempPasswordDisabled' | chpasswd \
    && addgroup ansible wheel \
    && echo '%wheel ALL=(ALL) NOPASSWD: ALL' > /etc/sudoers.d/wheel \
    && chmod 0440 /etc/sudoers.d/wheel \
    && mkdir -p /run/sshd /home/ansible/.ssh \
    && chown -R ansible:ansible /home/ansible \
    && chmod 700 /home/ansible/.ssh \
    && ssh-keygen -A

COPY sshd_config /etc/ssh/sshd_config

EXPOSE 80 22

CMD ["/bin/sh", "-c", "/usr/sbin/sshd && exec nginx -g 'daemon off;'"]