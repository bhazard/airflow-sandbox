ARG POSTGRES_VERSION=12.3-alpine
FROM postgres:$POSTGRES_VERSION

#ENV PGDATA=/var/lib/postgresql/data/pgdata
# Can't do this, as pg checks for empty data dir on start
#COPY conf/pg_hba.conf /var/lib/postgresql/data/pg_hba.conf
#COPY conf/postgresql.conf /var/lib/postgresql/data/postgresql.conf

# The bash script will update pg_hba.conf to allow external access (i.e.,
# access from a machine other than the HOST)
# RUN mkdir -p /docker-entrypoint-initdb.d
# COPY conf/allow_external_access.sh /docker-entrypoint-initdb.d

# CMD
