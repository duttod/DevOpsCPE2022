FROM httpd:2.4-alpine
COPY index2.html /usr/local/apache2/htdocs/index2.html
RUN chmod -R 755 /usr/local/apache2/htdocs/index2.html
COPY ./my-httpd.conf /usr/local/apache2/conf/httpd.conf
