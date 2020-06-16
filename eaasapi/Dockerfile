FROM debian
LABEL maintainer="info@carefuldata.com"
LABEL version="1.0"
LABEL description="EaaS API and Transfer Portal"

RUN apt-get update && apt-get install -y apt-transport-https aptitude
RUN aptitude install -y gpg sec openssl
COPY prod.cfg /etc/
RUN ln -sf /dev/stdout /var/log/product.log && mkdir /var/log/nginx/

CMD ["/usr/bin/sec", "--conf=/etc/prod.cfg", "--input=/var/log/nginx/access.log", "--log=/var/log/product.log"
