FROM postgres:11.6-alpine

COPY create_schema.sql /docker-entrypoint-initdb.d
COPY insert.sql /docker-entrypoint-initdb.d
ENV POSTGRES_DB=db \
POSTGRES_USER=usr \
POSTGRES_PASSWORD=pwd
