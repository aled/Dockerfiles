FROM wibblr/jessie-20140717-i386
MAINTAINER Aled Morris <aled@wibblr.com>
RUN apt-get update && apt-get -y upgrade
RUN apt-get -y install build-essential libreadline6-dev zlib1g-dev wget
RUN wget http://ftp.postgresql.org/pub/source/v9.4beta1/postgresql-9.4beta1.tar.bz2
RUN tar jxf postgresql-9.4beta1.tar.bz2

# this works on ubuntu trusty, but doesn't work on debian jessie
#RUN apt-get -y install language-pack-en-base

# this seems to work instead for debian jessie
RUN apt-get -y install locales
RUN echo "en_GB.UTF-8 UTF-8" > /etc/locales
RUN echo "en_GB.UTF-8 UTF-8" > /etc/locale.gen
RUN echo LANG=\"en_GB.UTF-8\" > /etc/default/locale
RUN echo LANGUAGE=\"en_GB:en\" >> /etc/default/locale
RUN /usr/sbin/locale-gen

RUN (cd /postgresql-9.4beta1 && ./configure && make install)
RUN apt-get -y remove build-essential libreadline6-dev zlib1g-dev wget
RUN apt-get -y autoremove
RUN apt-get clean
RUN rm -rf /postgresql-9.4beta1 /postgresql-9.4beta1.tar.bz2
RUN adduser -q --system --shell /bin/bash --disabled-password postgres
ENV PGDATA /usr/local/pgsql/data
ENV PGBIN /usr/local/pgsql/bin
RUN mkdir $PGDATA && chown postgres $PGDATA
USER postgres
RUN $PGBIN/initdb --locale=en_GB.utf8 -D $PGDATA
RUN $PGBIN/pg_ctl -w start && $PGBIN/psql --command "ALTER USER postgres WITH SUPERUSER PASSWORD 'postgres';" && $PGBIN/pg_ctl -w stop
RUN echo "listen_addresses = '*'" >> $PGDATA/postgresql.conf 
RUN echo 'host    all             all             0.0.0.0/0               md5' > $PGDATA/pg_hba.conf
EXPOSE 5432
CMD $PGBIN/postgres -c logging_collector=off
