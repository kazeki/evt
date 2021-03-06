FROM postgres:11.2

ARG POSTGRES_DEV_VERSION=11
ARG PATHMAN_VERSION=1.5.3

RUN set -x \
	&& apt-get update && apt-get install -y --no-install-recommends ca-certificates wget unzip make postgresql-server-dev-${POSTGRES_DEV_VERSION} gcc libc6-dev libssl-dev libkrb5-dev \
    && wget -O /tmp/pg_pathman.zip https://github.com/postgrespro/pg_pathman/archive/$PATHMAN_VERSION.zip \
	&& unzip /tmp/pg_pathman.zip  -d /tmp \
    && cd /tmp/pg_pathman-${PATHMAN_VERSION} \
    && make USE_PGXS=1 install \
    && rm -rf /var/lib/apt/lists/* && apt-get purge -y --auto-remove ca-certificates wget unzip make postgresql-server-dev-${POSTGRES_DEV_VERSION} gcc libc6-dev libssl-dev libkrb5-dev \
    && rm -rf /tmp/pg_*

ENV POSTGRES_USERNAME="postgres"
ENV POSTGRES_INITDB_ARGS="-D /var/lib/postgresql/data"
ENV PGDATA="/var/lib/postgresql/config"
ENV PGCONFIG="/var/lib/postgresql/config"
ENV AUTH="trust"

RUN mkdir -p $PGDATA
RUN cp /usr/share/postgresql/postgresql.conf.sample $PGDATA/postgresql.conf
RUN sed -i -e "s/#include_dir = 'conf.d'/include_dir = 'conf.d'/" $PGDATA/postgresql.conf
COPY conf.d $PGDATA/conf.d/

RUN mkdir -p /docker-entrypoint-initdb.d
COPY ./initdb-auth.sh /docker-entrypoint-initdb.d/00-auth.sh
COPY ./initdb-pg_pathman.sh /docker-entrypoint-initdb.d/01-pg_pathman.sh
