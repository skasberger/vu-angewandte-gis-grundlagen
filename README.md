VU Angewandte GIS Grundlagen
==============================

- Lehrperson: [Markus Pichler](https://online.uni-graz.at/kfu_online/visitenkarte.show_vcard?pPersonenId=9004CD948F5E4502&pPersonenGruppe=3)
- Semester: Wintersemester 2015/16
- Sprache: Deutsch
- [Webpage](https://online.uni-graz.at/kfu_online/wblv.wbShowLvDetail?pStpSpNr=410824&pSpracheNr=1&pMUISuche=FALSE)

**Verwendete Software**

Alles Open Source.
- [Ubuntu 14.04](http://ubuntu.com/)
- [postgreSQL](www.postgresql.org/) (9.3.13, Datenbank) mit [PostGIS](http://postgis.org/) (2.1, für Spatial Queries und Geometrien als Datentyp) und [pgRouting](http://pgrouting.org/) (2.2.3, für Routing Funktionen) Erweiterung. Dies ist somit eine komplette Open Source Datenbank um mit räumlichen Daten zu arbeiten.
- [pgAdmin 3](https://www.pgadmin.org/) (1.18.1): Client zum Entwickeln und Administrieren von postgreSQL Datenbanken.
- [QGIS](http://www.qgis.org/de/site/) (2.8.1): GIS Desktop Anwendung für Analysen und Erstellung von Karten.
- [osm2pgsql](http://learnosm.org/en/osm-data/osm2pgsql/) (0.82.0): Import Tool von OpenStreetMap Daten in postgreSQL Datenbanken mit PostGIS Erweiterung.
- [osm2pgRouting](http://pgrouting.org/docs/tools/osm2pgrouting.html) (2.1): Import Tool von OpenStreetMap Daten als routing-fähige Topology in eine postgreSQL Datenbank mit pgRouting Erweiterung.
- [Docker](https://www.docker.com/) (1.12.0) und  [pgRouting Image](ttps://hub.docker.com/r/starefossen/pgrouting/): Docker ist eine Art Virtual Machine und ermöglicht somit das Verwenden eines Container (Image) auf verschiedenen Betriebssystemen. Als Grundlage wurde ein bereits vorhandenes Image mit pgRouting genutzt.
- [git](http://git-scm.com/) (1.9.1): Versionierung Software. Wurde zum Versionieren des GitHub Repos und deren Inhalte genutzt.

**Verwendete Daten**

Es wurden offene Daten aus folgenden Datenportalen verwendet:
- [Open Government Data Graz](http://data.graz.gv.at/)
- [Overpass API](http://overpass-api.de/)

## ENDARBEIT

Bei der [Endarbeit](/endarbeit) wurden anhand von Open Government Data und OpenStreetMap Daten der kompletten Lebenszyklus von Daten, von der Erstellung bis zur wissenschaftlichen Analyse dargelegt. Zu Beginn wurde mit [Map your Hood](https://github.com/skasberger/map-your-hood) eine OpenStreetMap Mapping Party organisiert, wo Daten zu Kebab-Restaurants im Bezirk Gries in Graz im Gelände gesammelt und dann in die OpenStreetMap für alle zugänglich eingetragen wurde. Die so vervollständigten Daten wurden in einem weiteren Schritt runtergeladen und für eine Erreichbarkeits-Analyse für Fussgänger_innen genutzt. Die finale Datenbank wurde dann in ein Docker-Image geladen und im Web geteilt, das anderen ein einfaches nutzen und erweitern der Daten ermöglicht.

## AUFBAU
- [README.md](README.md): Übersicht zu Repo
- [/endarbeit](/endarbeit): Ordner mit allen Daten zur Endarbeit
