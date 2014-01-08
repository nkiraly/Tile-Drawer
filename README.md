Tile-Drawer
===========

Rapid deployment of an OpenStreetMap-based map tiles server.

What follows is a fast track to kickstarting an nginx + unicorn + memcached + mapnik + postgresql + postgis OpenStreetMaps tile server.

I assume things such as: You know how to install Ubuntu Server 13.10, you know how to allocate disk and RAM, and you want to be friends with an OSM tile server.



Anecdotal Knowledge
===================

You need a lot of disk space to import large spatial definitions of the planet. The whole planet is a 31GB PBF which becomes over 600GB in the final spatial RDBMS with all of it's derivative data.

During import, RAM is strongly preferred over abusing swap space to process all of the spatial data into the database.



Quick Setup Guide
=================

If you want to just get a tile server working and then learn how it works, select a PBF segment of the planet you want to serve from a partial extractor such as http://download.geofabrik.de/north-america.html and follow these top level steps. All commands should be run as root.


1). Setup Ubuntu 13.10 server with large storage at /mnt - You will need 6GB RAM minimum to import any size PBF due to the way osmosis and osm2pgsql use memory. I recommend 8GB to ensure when postgresql transactions are buffer draining you don't start swapping out. I also like to allocate 4GB of swap to deal with spikes in memory usage by the toolchain.

2). Install git to boostrap your installation, and update your packages to all by current and matching
```
apt-get -y install git curl htop vim
apt-get update
apt-get upgrade
```

3). Clone this repository
```
git clone -b config  https://github.com/nkiraly/Tile-Drawer.git /usr/local/tiledrawer
```

4). Install servers and toolchain
```
/usr/local/tiledrawer/setup.sh
```

5). Populate your database with planet spatial data or a partial extract of this data. For example if you want to use the Cascadenik classic styling, only loading US spatial extracts from geofabrik, use
```
/usr/local/tiledrawer/populate.py -b -85.0000 -180.0000 85.0000 180.0000 -s https://raw.github.com/mapnik/Cascadenik/tiledrawer/openstreetmap/style.mml http://download.geofabrik.de/north-america/us-northeast-latest.osm.pbf http://download.geofabrik.de/north-america/us-midwest-latest.osm.pbf http://download.geofabrik.de/north-america/us-pacific-latest.osm.pbf http://download.geofabrik.de/north-america/us-south-latest.osm.pbf http://download.geofabrik.de/north-america/us-west-latest.osm.pbf
```

6). Finalize config to serve tiles and slippymap from the HTTP root
```
/usr/local/tiledrawer/draw.sh
```

