FROM nginx:latest
LABEL maintainer="carefuldata@protonmail.copm"
LABEL version="1.0"
LABEL description="NGINX web server for Eaas API"

RUN mkdir -p /var/www/html/css/images /etc/letsencrypt/live/eanginx /var/www/html/images/

# Some ideas of things you might add in, but the template won't copy anything by default here.
# COPY options-ssl-nginx.conf /etc/letsencrypt/
# COPY index.html /var/www/html/
# COPY main.css /var/www/html/css/
# COPY logo.png /var/www/html/images/
# COPY nginx.conf /etc/nginx/
# COPY default /etc/nginx/sites-enabled/
# COPY fullchain.pem /etc/letsencrypt/live/eanginx/
# COPY privkey.pem /etc/letsencrypt/live/eanginx/
# COPY ssl-dhparams.pem /etc/letsencrypt/

# Template/contact info as default page:
COPY index.html /usr/share/nginx/html/index.html

EXPOSE 443 80

CMD ["nginx", "-g", "daemon off;"]
