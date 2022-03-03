FROM postgres:14.2
ENV TERM xterm-256color

RUN apt-get update && \
    apt-get --fix-missing -y --no-install-recommends install git ca-certificates && \
    git clone https://github.com/tada/pljava.git && \
    apt-get --fix-missing -y --no-install-recommends install g++ maven && \
    apt-get --fix-missing -y --no-install-recommends install postgresql-server-dev-14 libpq-dev && \
    apt-get --fix-missing -y --no-install-recommends install libecpg-dev libkrb5-dev && \
    apt-get --fix-missing -y --no-install-recommends install openjdk-11-jdk libssl-dev && \
    export PGXS=/usr/lib/postgresql/11/lib/pgxs/src/makefiles/pgxs.mk && \
    cd pljava && \
    mvn clean install && \
    java -jar $(ls /pljava/pljava-packaging/target/pljava-pg14.jar) && \
    cd ../ && \
    apt-get -y remove --purge --auto-remove git ca-certificates g++ maven postgresql-server-dev-14 libpq-dev libecpg-dev openjdk-11-jdk libkrb5-dev libssl-dev && \
    apt-get --fix-missing -y  --no-install-recommends install openjdk-11-jdk-headless && \
    apt-get -y clean autoclean autoremove && \
    rm -rf ~/.m2 /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY docker-entrypoint-initdb.d /docker-entrypoint-initdb.d
COPY scripts /scripts
RUN chmod -R +x '/scripts'


