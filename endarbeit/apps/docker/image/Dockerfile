FROM starefossen/pgrouting:latest

MAINTAINER Stefan Kasberger <mail@stefankasberger.at>

LABEL Version="0.1" Description="This text illustrates that label-values can span multiple lines." License="GPL"

RUN apt-get -y update
RUN apt-get install -y osm2pgsql 
# git
# RUN git clone https://github.com/skasberger/vu-angewandte-gis-grundlagen.git
ADD vugis.sql /docker-entrypoint-initdb.d/
#RUN psql -U postgres -h postgres -c "CREATE DATABASE vugis"
#RUN psql -U postgres -h postgres vugis < vu-angewandte-gis-grundlagen/endarbeit/data/sql/vugis.sql

#VOLUME ["/my-data/science/academia/vu-gis-grundlagen/endarbeit/data/docker:/data"]


