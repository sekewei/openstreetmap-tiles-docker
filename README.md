# OpenStreetMap Tile Server Container

This repository contains instructions for building a
[Docker](https://www.docker.io/) image containing the OpenStreetMap tile
serving software stack.  It is based on the
[Switch2OSM instructions](http://switch2osm.org/serving-tiles/manually-building-a-tile-server-12-04/).

As well as providing an easy way to set up and run the tile serving software it
also provides instructions for managing the back end database, allowing you to:

* Create the database
* Import OSM data into the database
* Drop the database

Run `docker run homme/openstreetmap-tiles` for usage instructions.

## About

The container runs Ubuntu 14.04 (Trusty) and is based on the
[phusion/baseimage-docker](https://github.com/phusion/baseimage-docker).  It
includes:

* Postgresql 9.3
* Apache 2.2
* The latest [Osm2pgsql](http://wiki.openstreetmap.org/wiki/Osm2pgsql) code (at
  the time of image creation)
* The latest [Mapnik](http://mapnik.org/) code (at the time of image creation)
* The latest [Mod_Tile](http://wiki.openstreetmap.org/wiki/Mod_tile) code (at
  the time of image creation)

## Issues

This is a work in progress and although generally adequate it could benefit
from improvements.  Please
[submit issues](https://github.com/geo-data/openstreetmap-tiles-docker/issues)
on GitHub. Pull requests are very welcome!

## Usage

# install the docker context from git and build your own docker image
git clone https://github.com/sekewei/openstreetmap-tiles-docker.git
or download from https://github.com/sekewei/openstreetmap-tiles-docker

cd openstreetmap-tiles-docker/
docker build -t twstd:latest .

# run the bare map server, load the data and services, and save the image
docker run -it -v /home/seke/docker/data/osm-postgresql:/var/lib/postgresql	--name twstd twstd:v0  /bin/bash

docker attach twstd

cd /usr/local/sbin/
./run initdb
./run startdb
./run createuser
./run createdb
wget -O /data/import.pbf http://download.geofabrik.de/asia/taiwan-latest.osm.pbf
./run import
Ctrl-p + Ctrl-q

docker commit -m full_loaded twstd twstd:v1

# launch the map server and test on the browser
docker run -it -p 80:80  --name twstd  twstd:v1
cd /usr/local/sbin/
./run startservices

http://localhost/
click the item '開放街圖 openstreetmap'

