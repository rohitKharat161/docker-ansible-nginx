
FROM nginx:alpine

RUN apk add --no-cache iptables

COPY firewall.sh /usr/local/bin/firewall.sh

RUN chmod +x /usr/local/bin/firewall.sh

CMD ["/usr/local/bin/firewall.sh"]
