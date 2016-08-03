Endarbeit VU Angewandte GIS Grundlagen
==============================

Dies ist die Endarbeit der Lehrveranstaltung VU Angewandte GIS-Grundlagen.

Die Aufgabenstellung wurde selber ausgearbeítet. Ziel war es, den kompletten Datenverarbeitungsprozess durch die Erstellung, Nutzung und Analyse von räumlichen Informationen anhand von OpenStreetMap zu erlernen. Dadurch sollte der gesellschaftliche Nutzen von Open Data und Open Source durch den kompletten Daten-Zyklus sichtbar gemacht und auch selber gleich dazu beigetragen werden.

Bei der Aufgabe wurden anhand von Open Government Data und OpenStreetMap Daten der kompletten Lebenszyklus von Daten, von der Erstellung bis zur wissenschaftlichen Analyse dargelegt. Zu Beginn wurde mit Map your Hood eine OpenStreetMap Mapping Party organisiert, wo Daten zu Kebab-Restaurants im Bezirk Gries in Graz im Gelände gesammelt und dann in die OpenStreetMap für alle zugänglich eingetragen wurde. Die so vervollständigten Daten wurden in einem weiteren Schritt runtergeladen und für eine Erreichbarkeits-Analyse für Fussgänger_innen mittels einer postgreSQL Datenbank mit räumlichen Erweiterungen verwaltet. Die finale Datenbank wurde dann in ein Docker-Image geladen und im Web geteilt, was ein einfaches nutzen und erweitern der Daten für andere ermöglicht.

Zentrale Arbeitsgrundlage waren die Tutorials von [Anita Graser](https://anitagraser.com/) sowie Workshop-Materialien. Vielen Dank!
- [pgRouting Workshop](http://workshop.pgrouting.org/index.html)
- [pgRouting Tutorial](http://workshops.boundlessgeo.com/tutorial-routing/)
- [A Beginners Guide to pgrouting](https://anitagraser.com/2011/02/07/a-beginners-guide-to-pgrouting/)
- [Drive Time Isochrones – An Example Using Finnish Airports](https://anitagraser.com/2011/02/12/drive-time-isochrones/)
- [Creating Catchment Areas with pgRouting and QGIS](https://anitagraser.com/2011/02/09/creating-catchment-areas-with-pgrouting-and-qgis/)

**Inhalt**
1. Map your Hood
2. Setup
3. Daten aufbereiten
4. Erreichbarkeit berechnen
5. Docker Image erstellen
6. Karte erstellen
- Beitragen
- Urheber_innenrecht
- Todo
- Quellen
- Aufbau Repository

**Verwendete Software**

- [Ubuntu 14.04](http://ubuntu.com/)
- [postgreSQL](www.postgresql.org/) (9.3.13, Datenbank) mit [PostGIS](http://postgis.org/) (2.1, für Spatial Queries und Geometrien als Datentyp) und [pgRouting](http://pgrouting.org/) (2.2.3, für Routing Funktionen) Erweiterung. Dies ist somit eine komplette Open Source Datenbank um mit räumlichen Daten zu arbeiten.
- [pgAdmin 3](https://www.pgadmin.org/) (1.18.1)
- [QGIS](http://www.qgis.org/de/site/) (2.8.1): GIS Desktop Anwendung für Analysen und Erstellung von Karten.
- [osm2pgsql](http://learnosm.org/en/osm-data/osm2pgsql/) (0.82.0): Import Tool von OpenStreetMap Daten in postgreSQL Datenbanken mit PostGIS Erweiterung.
- [osm2pgRouting](http://pgrouting.org/docs/tools/osm2pgrouting.html) (2.1): Import Tool von OpenStreetMap Daten als routing-fähige Topology in eine postgreSQL Datenbank mit pgRouting Erweiterung.
- [Docker](https://www.docker.com/) (1.12.0) und  [pgRouting Image](ttps://hub.docker.com/r/starefossen/pgrouting/): Docker ist eine Art Virtual Machine und ermöglicht somit das Verwenden eines Container (Image) auf verschiedenen Betriebssystemen. Als Grundlage wurde ein bereits vorhandenes Image mit pgRouting genutzt.
- [git](http://git-scm.com/) (1.9.1): Versionierungs-Software. Wurde zum Versionieren des GitHub Repos und deren Inhalte genutzt.
- [wget](https://www.gnu.org/software/wget/) (1.15)

**Verwendete Daten**

Es wurden offene Daten aus folgenden Datenportalen verwendet:
- [Orthophotos Graz](http://data.graz.gv.at/daten/package/orthophotos)
- [Overpass API](http://overpass-api.de/)
- [Bezirksgrenzen Graz](https://github.com/species/OGD-Graz-Daten/blob/master/Bezirksgrenzen/Bezirksgrenzen.geojson)

**Urheber_innenrecht**

Alle Werke (Daten, Quellcode, Content) die in dieser Aufgabe verwendet oder erzeugt wurden, stehen unter einer freien Lizenz. Mehr Infos gibt es unter dem Punkt Urheber_innenrecht.

## 1. Map your Hood
OpenStreetMap Mapping Party im Bezirk Gries.
Teilnehmer_innen: XX
Was war das Ziel, was wurde gemacht.

Map your hood! war eine OpenStreetMap Mapping Party im Bezirk Gries in Graz, in dem Restaurants und Cafes gesammelt und in die OpenStreetMap eingetragen wurden. OpenStreetMap ist die Wikipedia für geographische Informationen, und steht somit allen frei zum Beitragen und Nutzen zur Verfügung. Zuerst gab es eine Einführung in den OpenStreetMap Editor [JOSM](https://josm.openstreetmap.de/), bevor es ins Gelände raus gegangen ist und die kulinarische Szene von Gries erfasst wurde. Die gesammelten Daten wurden am Ende gemeinsam in die OpenStreetMap eingetragen bevor die Ergebnisse auf [openstreetmap.org](http://openstreetmap.org/) betrachtet wurden.

- Datum: 25. März 2016, 14-18 Uhr
- Ort: Büro der Nachbarschaften, Gries, Graz
- Betreuer: [Stefan Kasberger](http://stefankasberger.at) (Open Knowledge Austria) und Michael Maier (OpenStreetMap Graz)
- [Website](http://okfn.at/2016/03/15/map-your-hood/)
- Veranstaltung: [Open Knowledge Austria](http://okfn.at) und [OpenStreetMap Graz](https://wiki.openstreetmap.org/wiki/Graz) in Kooperation mit der [Studienvertretung Geographie der Uni Graz](http://geographie.uni-graz.at/) und dem Projekt [Open Science @ Uni Graz](http://openscienceasap.org/projects-resources/open-science-at-uni-graz/) des Alternativ-Referats der ÖH Uni Graz.

Eine detailierte Erklärung was an dem Tag gemacht wurde ist in dem dafür eigenen **[GitHub Repository](https://github.com/skasberger/map-your-hood
)**  zu finden.

## 2. SETUP
Zwei Möglichkeiten:

1. Dieses Repository auf den lokalen Rechner klonen. Dann kann man auch Verbesserungen oder Erweiterungen am Repository vornehmen.
```bash
git clone git@github.com:skasberger/vu-angewandte-gis-grundlagen.git
```

2. Repository runterladen: Screenshot oder Erklären auf GitHub
```bash
wget --progress=dot:mega "https://github.com/skasberger/vu-angewandte-gis-grundlagen/archive/master.zip"
```

Setzen der ROOT_DIRECTORY Variable. Dazu muss zuerst in das Wurzelvereichnis des Repositories gegangen werden, bevor in den Ordner für die Endarbeit gegangen wird.
```bash
cd endarbeit/
ROOT_DIRECTORY=$(pwd)
```

### postgreSQL mit PostGIS und pgRouting

[postgreSQL](www.postgresql.org/) mit [PostGIS](http://postgis.org/) und [pgRouting](http://pgrouting.org/) ist der komplette GIS-Stack für die Speicherung und Verwaltung der Daten auf dem lokalen Rechner sowie im Docker Image. Installation siehe Dokumentation.

Zuerst muss eine Datenbank für das Projekt erstellt werden:
```bash
psql -h localhost -U postgres -c "CREATE DATABASE vugis;"
psql -h localhost -U postgres -c "CREATE SCHEMA osm; CREATE SCHEMA osm_pg_raw; CREATE SCHEMA data_raw; CREATE SCHEMA data;"
```

Damit die räumliche Funktionen mitsamt Routing funktionieren, müssen die PostGIS und pgRoutig Erweiterungen in der Datenbank aktiviert werden:
```bash
psql -h localhost -U postgres -d vugis -c "CREATE EXTENSION postgis; CREATE EXTENSION postgis_topology; CREATE EXTENSION pgrouting; CREATE EXTENSION hstore;"
```

Zuletzt wird noch die [clone_schema](https://wiki.postgresql.org/wiki/Clone_schema) Funktion für spätere Schritte hinzugefügt.

### pgAdmin

[pgAdmin 3](https://www.pgadmin.org/) ist ein Client zum Entwickeln und Administrieren von postgreSQL Datenbanken. Dies ermöglicht einfaches und komfortables kontrollieren und bearbeiten der Daten in den postgreSQL Datenbanken, was besonders zum Debuggen und Prototypen sehr hilfreich ist. Installation siehe Dokumentation.

Um dies nutzen zu können, muss lediglich die Verbindung zur lokalen Datenbank bzw. zum Docker Container hergestellt werden. Hierzu ist die IP-Adresse, der Nutzername sowie das dazugehörige Passwort notwendig.

### QGIS

[QGIS](http://www.qgis.org/de/site/) ist das Open Source Pendant zu ArcGis, also eine vollständige Desktop Anwendung, die alle Kernfunktionen der Analyse, Datenbearbeitung und Präsentation kann. Im Zuge der Dokumentation hier werden Screenshots aus QGIS immer wieder verwendet, um die getätigten Schritte visuell erkenntlich zu machen. Installation siehe Dokumentation.

## 3. DATEN AUFBEREITEN

### Bezirksgrenze Gries importieren
Die [Bezirksgrenzen Graz](https://github.com/species/OGD-Graz-Daten/blob/master/Bezirksgrenzen/Bezirksgrenzen.geojson) von Species GitHub Repo als geojson runterladen. Der WFS Service des Open Government Data Portals der Stadt Graz geht nämlich leider gerade nicht.

Die geojson-Datei in QGIS importieren (Drag & Drop) und als Shape-Datei speichern bzw. direkt in die postgreSQL Datenbank importieren.
![QGIS](/assets/images/endarbeit/export-geojson.png)

Das Shape-File via [shp2pgsql](http://www.geoportal.rlp.de/mediawiki/index.php/Shp2pgsql) in die PostGIS Datenbank importieren. Dabei muss explizit das CRS angegeben werden (31256). Bei den  Imports mit shp2pgsql ist immer auf das Koordinaten-System der Datei zu achten, dies ist bei den späteren Verwendungen zu beachten.

```bash
shp2pgsql -I -s 31256 $ROOT_DIRECTORY/data/shape/bezirksgrenzen/bezirksgrenzen.shp data_raw.bezirksgrenzen | psql -h localhost -U postgres -d data_raw.vugis
```

Bezirk Gries auswählen und als neue Tabelle speichern.
```sql
CREATE TABLE data.bezirksgrenze_gries AS SELECT * FROM data_raw.bezirksgrenzen WHERE bez_name = 'Gries';
```

Gries als Shape File exportieren.

![geoJSON als Shape exportieren](/assets/images/endarbeit/export-geojson.png)
Bild: Export der geoJSON Datei als Shape-File

### OpenStreetMap Daten (Kebab Restaurants) importieren
Beachte: Die Boundary Box sollte so klein wie möglich, aber so groß wie nötig gestaltet werden. Am besten ist es, einen kleinen Buffer Bereich rund um die benötigte Box zu machen (zB. 500m). Es werde dabei eh alle Geometrien, die innerhalb der Box sind und darüber hinausgehen runter geladen.

- osm daten via [overpass turbo API](https://overpass-turbo.eu/) runter laden. Die API ermöglicht einem komfortables Runterladen von OpenStreetMap Daten innerhalb einer definierten Boundary Box. Diese kann natürlich nach den eigenen Bedürfnissen angepasst und so andere Räume runtergeladen werden. Somit ist dies der wichtigste Schritt um die folgenden Analysen auf andere Räume anzuwenden, da alle weiteren Schritte auf den Daten die hier runtergeladen werden aufbauen (bis auf natürlich die Bezirksgrenzen).
```bash
BBOX="15.402231216430662,47.034800577106964,15.452785491943358,47.07509065142444"
wget --progress=dot:mega -O "$ROOT_DIRECTORY/data/raw/osm/bbox.osm" "http://www.overpass-api.de/api/xapi?*[bbox=${BBOX}][@meta]"
```

![Download OpenStreetMap Daten](/assets/images/endarbeit/download-osm-data.png)
Bild: Download der OpenStreetMap Boundary Box (bbox).

Das Tool [osm2pgsql](http://learnosm.org/en/osm-data/osm2pgsql/) installieren und die Style File anpassen. Es werden folgende Attribute der OpenStreetMap Datenbank für die weiteren Analysen gebraucht:
- name
- addr:housenumber
- addr:street
- cuisine
- amenity

![bbox Strassengraph](/assets/images/endarbeit/bbox-strassengraph.png)
Bild: Strassengraph der bbox nach pgRouting Import

Die OpenStreetMap Daten werden dann mittels osm2pgsql in die PostGIS Datenbank importiert.
```bash
osm2pgsql -H localhost -p bbox -s -E 3857 -U postgres -d vugis -S $ROOT_DIRECTORY/apps/osm2pgsql/default.style /my-data/science/academia/vu-gis-grundlagen/endarbeit/data/raw/osm/bbox.osm
```

Die importierten Tabellen in das OSM Schema in postgreSQL rüber kopieren.
```sql
DROP SCHEMA osm_raw CASCADE;

SELECT clone_schema('public','osm_raw');
```

Damit später Spatial Queries ([ST_WITHIN](http://postgis.net/docs/ST_Within.html)) gemacht werden können, muss noch das Koordinaten-System der point Tabelle transformiert ([ST_TRANSFORM](http://postgis.net/docs/ST_Transform.html)) werden (SRID = 31256). Dazu wird die bestehende Tabelle kopiert und die Geometrie danach abgeändert - alles in PostGIS.
```sql
CREATE TABLE osm.bbox_point_31256 AS SELECT * FROM osm_raw.bbox_point;

ALTER TABLE osm.bbox_point_31256
  ALTER COLUMN way
  TYPE Geometry(Point, 31256)
  USING ST_Transform(way, 31256);
```

![OpenStreetMap Points der bbox](/assets/images/endarbeit/bbox-points.png)
Bild: OpenStreetMap Daten am Beispiel der Point Tabelle.

Als nächstes werden, mittels eines Spatial Query in PostGIS, die Punkte aus Gries ausgewählt.
```sql
CREATE TABLE osm.gries_point_31256 AS SELECT * FROM osm.bbox_point_31256, data.bezirksgrenze_gries WHERE ST_WITHIN(way, data.bezirksgrenze_gries.geom);
```

![Points Gries](/assets/images/endarbeit/gries-points.png)
Bild: Points im Bezirk Gries

Nun muss noch nach cuisine = kebab in all seinen Varianten gefiltert werden. postgreSQL Query:
```sql
CREATE TABLE osm.gries_kebab_31256 AS SELECT * FROM osm.gries_point_31256 WHERE cuisine = 'kebab' OR cuisine = 'kebab,pizza' OR cuisine = 'kebab;pizza' OR cuisine = 'kebab;pizza;ice_cream' OR cuisine = 'kebap, pizza' OR cuisine = 'kebap,pizza' OR cuisine = 'kebap;pizza';
```

![alt text](/assets/images/endarbeit/kebab-restaurants.png)
Bild: Kebab Restaurants im Bezirk Gries

### OpenStreetMap Daten (Strassengraph) importieren
**mit osm2pgRouting**

Die OpenStreetMap Daten in pgRouting importieren:
```bash
osm2pgrouting --f $ROOT_DIRECTORY/data/raw/osm/bbox.osm --dbname vugis --username postgres --schema osm_pg_raw --clean
```
Folgende Tabellen werden dadurch erstellt:
- osm_nodes
- osm_relations
- osm_way_classes
- osm_way_types
- osm_way_tags
- osm_way types
- relations_ways
- ways => dies ist die routingfähige Tabelle
- way_vertices_pgr

Um die ways Tabelle auf den Raum Gries einzugrenzen wird zuerst ein Buffer mit 200m um die Bezirksgrenze von Gries gelegt, um direkt darum liegende Wege noch in die Berechnung miteinzubeziehen. Dies wird in PostGIS gemacht.

```sql
CREATE TABLE data.bezirksgrenze_gries_buffer AS SELECT ST_Buffer(geom,200) AS geom FROM data.bezirksgrenze_gries;
```

![alt text](/assets/images/endarbeit/gries-buffer-ways.png)
Bild: Der erweiterte Buffer-Bereich rund um die Bezirksgrenze von Gries.

Damit später Spatial Queries ([ST_WITHIN](http://postgis.net/docs/ST_Within.html)) gemacht werden können, muss noch das Koordinaten-System der ways Tabelle transformiert ([ST_TRANSFORM](http://postgis.net/docs/ST_Transform.html)) werden (SRID = 31256). Dazu wird die bestehende Tabelle kopiert und die Geometrie danach abgeändert - alles in PostGIS.

```sql
CREATE TABLE osm.bbox_ways_31256 AS SELECT * FROM osm_pg_raw.ways;

ALTER TABLE osm.bbox_ways_31256
  ALTER COLUMN the_geom
  TYPE Geometry(LineString, 31256)
  USING ST_Transform(the_geom, 31256);
```

![OpenStreetMap bbox Ways](/assets/images/endarbeit/bbox-ways.png)
Bild: OpenStreetMap Daten am Beispiel der osm.bbox_ways Tabelle.

Zuerst müssen wir auch hier wieder die Geometrien in das CRS 31256 transformieren.
```sql
CREATE TABLE osm.bbox_ways_vertices_pgr_31256 AS SELECT * FROM osm_pg_raw.ways_vertices_pgr;

ALTER TABLE osm.bbox_ways_vertices_pgr_31256
  ALTER COLUMN the_geom
  TYPE Geometry(Point, 31256)
  USING ST_Transform(the_geom, 31256);
```

Als nächstes werden, mittels eines Spatial Query in PostGIS, die ways und vertices aus Gries ausgewählt.
```sql
CREATE TABLE osm.gries_ways_31256 AS SELECT * FROM osm.bbox_ways_31256, data.bezirksgrenze_gries_buffer WHERE ST_WITHIN(the_geom, data.bezirksgrenze_gries_buffer.geom);
CREATE TABLE osm.gries_ways_vertices_pgr_31256 AS SELECT * FROM osm.bbox_ways_vertices_pgr_31256, data.bezirksgrenze_gries_buffer WHERE ST_WITHIN(the_geom, data.bezirksgrenze_gries_buffer.geom);
```

Bereinigen der Spalten bevor man Shortest Path Routing Algorithmen verwendet.
```sql
CREATE TABLE osm.gries_ways_clean_31256 AS SELECT gid::integer AS id, class_id, length, length_m, name, source::integer, target::integer, x1, y1, x2, y2, cost::double precision, reverse_cost, cost_s, reverse_cost_s, rule, one_way, maxspeed_forward, maxspeed_backward, osm_id, source_osm, target_osm, priority, the_geom FROM osm.gries_ways_31256;
```

Fertiges Dateset in QGIS.

![Gries OpenStreetMap Ways](/assets/images/endarbeit/gries-ways.png)
Bild: OpenStreetMap Daten am Beispiel der osm.gries_ways_31256 Tabelle.

## 4. ERREICHBARKEIT BERECHNEN

Um die Catchment Area darstellen zu können, muss die kürzeste Distanz aller Shortest Paths zwischen den Kebab-Restaurants und allen Knoten des Strassengraphen berechnet werden.

Zuerst muss zu jedem Kebab-Restaurant der am nächsten gelegene Knoten des Strassengraphen gefunden werden. Dessen osm_id wird dann als osm.gries_kebab_31256.nearest_node_osm_id abgespeichert.
[ST_DISTANCE](http://postgis.net/docs/ST_Distance.html)
```sql
CREATE TABLE osm.temp AS
 	SELECT a.osm_id AS kebab_osm_id, b.vertice_osm_id, min(a.dist) FROM (
 		SELECT osm.gries_kebab_31256.osm_id, min(st_distance(osm.gries_kebab_31256.way, osm.gries_ways_vertices_pgr_31256.the_geom)) AS dist
 			FROM osm.gries_kebab_31256, osm.gries_ways_vertices_pgr_31256
 			GROUP BY osm.gries_kebab_31256.osm_id
 		) AS a, (
 		SELECT osm.gries_kebab_31256.osm_id AS kebab_osm_id, osm.gries_ways_vertices_pgr_31256.osm_id AS vertice_osm_id, st_distance(osm.gries_kebab_31256.way, osm.gries_ways_vertices_pgr_31256.the_geom) AS dist
 			FROM osm.gries_kebab_31256, osm.gries_ways_vertices_pgr_31256) AS b
 	WHERE a.dist = b.dist AND a.osm_id = b.kebab_osm_id
 	GROUP BY a.osm_id, b.vertice_osm_id;

ALTER TABLE osm.gries_kebab_31256
	ADD COLUMN nearest_node_osm_id bigint;

UPDATE osm.gries_kebab_31256
SET nearest_node_osm_id =
   (SELECT vertice_osm_id
    FROM osm.temp
    WHERE osm.temp.kebab_osm_id = osm.gries_kebab_31256.osm_id);

DROP TABLE osm.temp;
```

Die gefundenen Knoten werden nun für als Start-Knoten für den [Dijkstra Shortest Path Algorithmus](http://docs.pgrouting.org/2.2/en/src/dijkstra/doc/pgr_dijkstra.html#pgr-dijkstra) übergeben. Um das Ergebnis einfach verwenden zu können, wird als Kosten gleich die Länge in Metern übergeben.

| name                 | osm_id          | nearest_node_osm_id     | nearest_node_id |
| -------------------- | --------------- | ----------------------- | --------------- |
| Halici               | 2341231657      | 2341231885              | 9433 |
| Beyti                | 4085622493      | 2679570643              | 14099 |
| Istanbul LOKANTASI   | 4078507629      | 20959668                | 17006 |
| Anten Sofrasi        | 4078495096      | 3931378171              | 6457 |
| Kara                 | 3931378157      | 3931378171              | 6457 |
| Akamese König Kebap  | 3047975626      | 2048044041              | 2274 |
| Ankara Grillhaus     | 1369086923      | 2048167264              | 4704 |
| Star Kebap           | 1978233152      | 21525924                | 2698 |

```sql
CREATE TABLE osm.catchment_kebab_31256 (
    id bigint,
    osm_id bigint,
    cost double precision
);

SELECT AddGeometryColumn('osm', 'catchment_kebab_31256', 'the_geom', 31256, 'POINT', 2);

INSERT INTO osm.catchment_kebab_31256 (
  SELECT id, osm_id, (
		SELECT sum(cost) FROM (
			SELECT * FROM pgr_dijkstra('SELECT id, source, target, length_m::int4 AS cost FROM osm.gries_ways_clean_31256', 9433, id, false)
		) AS foo
	) AS cost, the_geom FROM osm.gries_ways_vertices_pgr_31256);

INSERT INTO osm.catchment_kebab_31256 (
  SELECT id, osm_id, (
		SELECT sum(cost) FROM (
			SELECT * FROM pgr_dijkstra('SELECT id, source, target, length_m::int4 AS cost FROM osm.gries_ways_clean_31256', 14099, id, false)
		) AS foo
	) AS cost, the_geom FROM osm.gries_ways_vertices_pgr_31256);

INSERT INTO osm.catchment_kebab_31256 (
  SELECT id, osm_id, (
		SELECT sum(cost) FROM (
			SELECT * FROM pgr_dijkstra('SELECT id, source, target, length_m::int4 AS cost FROM osm.gries_ways_clean_31256', 17006, id, false)
		) AS foo
	) AS cost, the_geom FROM osm.gries_ways_vertices_pgr_31256);

INSERT INTO osm.catchment_kebab_31256 (
  SELECT id, osm_id, (
		SELECT sum(cost) FROM (
			SELECT * FROM pgr_dijkstra('SELECT id, source, target, length_m::int4 AS cost FROM osm.gries_ways_clean_31256', 6457, id, false)
		) AS foo
	) AS cost, the_geom FROM osm.gries_ways_vertices_pgr_31256);

INSERT INTO osm.catchment_kebab_31256 (
  SELECT id, osm_id, (
		SELECT sum(cost) FROM (
			SELECT * FROM pgr_dijkstra('SELECT id, source, target, length_m::int4 AS cost FROM osm.gries_ways_clean_31256', 2274, id, false)
		) AS foo
	) AS cost, the_geom FROM osm.gries_ways_vertices_pgr_31256);

INSERT INTO osm.catchment_kebab_31256 (
  SELECT id, osm_id, (
		SELECT sum(cost) FROM (
			SELECT * FROM pgr_dijkstra('SELECT id, source, target, length_m::int4 AS cost FROM osm.gries_ways_clean_31256', 4704, id, false)
		) AS foo
	) AS cost, the_geom FROM osm.gries_ways_vertices_pgr_31256);

INSERT INTO osm.catchment_kebab_31256 (
  SELECT id, osm_id, (
		SELECT sum(cost) FROM (
			SELECT * FROM pgr_dijkstra('SELECT id, source, target, length_m::int4 AS cost FROM osm.gries_ways_clean_31256', 2698, id, false)
		) AS foo
	) AS cost, the_geom FROM osm.gries_ways_vertices_pgr_31256);
```

Aus den verschiedenen Werten die ein jeder Strassen-Knoten bei den Berechnungen oben bekommen hat, muss nun noch der minimalste gefunden und in eine eigene Tabelle gespeichert werden.
```sql
CREATE TABLE osm.catchment_kebab_final_31256 AS
  SELECT id, the_geom, min(cost) AS cost FROM osm.catchment_kebab_31256
  GROUP BY id, the_geom;
```

![Strassengraph-Knoten mit Kosten](/assets/images/endarbeit/costs-points.png)
Bild: Die Knoten des Strassengraphen mit der minimalen Distance dargestellt (abgestuft in 5, 10, 20, 30 und 60min).

![Strassengraph-Knoten mit Kosten](/assets/images/endarbeit/costs-points-groß.png)
Bild: Die Knoten des Strassengraphen mit der minimalen Distance in Nahaufnahme (abgestuft in 5, 10, 20, 30 und 60min).

## 5. ERSTELLEN DES DOCKER IMAGES

### Datenbank exportieren
Die gesamte Datenbank in eine SQL Datei dumpen, welche danach einfach Importiert werden kann
```bash
pg_dump -h localhost -U postgres -d vugis > $ROOT_DIRECTORY/data/sql/vugis.sql
```

SQL file in Docker Folder kopieren.
```bash
cp /$ROOT_DIRECTORY/data/sql/vugis.sql $ROOT_DIRECTORY/data/docker
```

### Import

ip: 172.17.0.2
user: postgres
pw: wie eingestellt

```bash
POSTGRES_PASSWORD=YOUR_PASSWORD
DOCKER_IMAGE_ID=YOUR_IMAGE_ID
```

start pgrouting container as daemon
```bash
docker run --name pgrouting-daemon-2 -e POSTGRES_PASSWORD=$POSTGRES_PASSWORD -d starefossen/pgrouting
```

start postgresql in shell
```bash
docker run -it --link pgrouting-daemon-2:postgres -v /my-data/science/academia/vu-gis-grundlagen/endarbeit/data/docker:/data --rm postgres /bin/bash
```

Create vugis Datenbank
```bash
psql -U postgres -h postgres -c "CREATE DATABASE vugis"
```

Importieren des Dumps.
```bash
psql -U postgres -h postgres vugis < /data/vugis.sql
```

Wie findet man die IP raus?
```bash
docker exec $DOCKER_IMAGE_ID cat /etc/hosts
```

## 6. KARTE ERSTELLEN
QGIS öffnen. 31256 ist das CRS.

![QGIS](/assets/images/endarbeit/qgis-leer.png)
Bild: QGIS nach dem Starten.

Verbindung mit dem Docker Container aufbauen.

![Docker Verbindung](/assets/images/endarbeit/docker-verbindung.png)
Bild: Verbindung zur postgreSQL Datenbank des Docker Containers aufbauen.

Die [Orthophotos Graz](http://data.graz.gv.at/daten/package/orthophotos) als Hintergrund laden.

![Orthophotos Graz](/assets/images/endarbeit/orthophoto-graz.png)
Bild: Importieren des Orthophoto Graz als Hintergrund-Layer.

Bezirksgrenze Gries importieren und mit dicken Aussengrenzen sowie transparenter Füllung darstellen

![Bezirksgrenze Gries](/assets/images/endarbeit/gries.png)
Bild: Die Bezirksgrenze von Gries.

Strassen importieren und passend anzeigen

![Gries OpenStreetMap Ways](/assets/images/endarbeit/gries-ways.png)
Bild: Strassengraph nach osm2pgrouting Import.

![Gries OpenStreetMap Ways Nahaufnahme](/assets/images/endarbeit/gries-ways-groß.png)
Bild: Strassengraph nach osm2pgrouting Import in Nahaufnahme.

Kebab Restaurants importieren und passend anzeigen

![Kebab Restaurants Gries](/assets/images/endarbeit/kebab-restaurants.png)
Bild: Kebab Restaurants in Gries.

Catchment Areas berechnen und darstellen

Um die Erreichbarkeit für Fußgänger darstellen zu können, muss zuerst die Distanz die in den jeweiligen Zeiten gegangen werden errechnet werden. Bei einer Durchschnittsgeschwindigkeit von 4km/h bedeutet dies:

| min | meter |
| --- | --- |
| 5 | 333 |
| 10 | 666 |
| 20 | 1333 |
| 30 | 2000 |
| 60 | 4000 |

![alt text](/assets/images/endarbeit/)
Bild: Einstellung Plugin

![Catchment Area](/assets/images/endarbeit/catchment-tiff.png)
Bild: Catchment Areas nach Berechnung

![Einstellungen Kontur-Plugin](/assets/images/endarbeit/catchment-contour-settings.png)
Bild: Einstellung der Kontur-Linien

![Catchment Area mit Kontur-Linien](/assets/images/endarbeit/catchment-countours.png)
Bild: Catchment Areas mit Kontur-Linien

Erstellen einer Druckzusammenstellung.

SCREENSHOT QGIS
![alt text](/assets/images/endarbeit/)
Bild:

Export als PDF, PNG und SVG

![alt text](/assets/images/endarbeit/)
Bild: SVG oder PNG Export des Ergebnisses

## SONSTIGES

### Snippets
Import OpenStreetMap Daten von Österreich (.osm.pbf) via osm2pgsql
```bash
osm2pgsql --create --database osm.vugis austria-latest.osm.pbf
```

Check postgis Version
```bash
psql -U postgres -h localhost -d vugis -c "SELECT postgis_version()"
```

Check pgRouting Version
```bash
psql -U postgres -h localhost -d vugis -c "SELECT pgr_version()"
```

## BEITRAGEN

Im Sinne des Freien Software Gedankens ist jede_r eingeladen dieses Projekt zu verbessern.

Hier einige Möglichkeiten dazu beizutragen:

- Bugs (Fehler) melden
- in andere Sprachen übersetzen
- Dokumentation schreiben und verbessern
- neue Analysen mit den Daten
- die Daten visualisieren
- Code schreiben(**kein Pull Request ist zu klein**: Schreibfehler korrigieren, Kommentare hinzufügen,...)
- Code refaktorieren
- Issues schliessen
- Pull Requests reviewen
- Daten mit weiteren Daten anreichern

Wenn du was machen willst, reiche einen [pull request](https://github.com/OKFNat/armScraper/pulls) ein.

### Issue erstellen

Wir verwenden den [GitHub issue tracker](https://github.com/OKFNat/armScraper/issues) um Bugs und Features zu verwalten. Bevor du einen Bug Report oder einen Feature Request absendest, bitte nachsehen ob dies nicht bereits gemeldet worden ist. Wenn du einen Bug meldest füge bitte einen Screenshot hinzu um das Problem zu beschreiben.

## URHEBER_INNENRECHT

Alle Werke sind offen lizenziert unter der [Creative Commons Namensnennung 4.0](http://creativecommons.org/licenses/by/4.0/) Lizenz, soweit nicht anders erwähnt.

Der Quellcode ist freie Software: du kannst ihn verbreiten und/oder abändern unter den Bedingungen der MIT Lizenz.

Der Quellcode wird zur Verfügung gestellt, unter der Hoffnung dass er nützlich ist, aber OHNE JEGLICHE HAFTUNG; auch ohne der implizierten Haftung der GEWÄHRLEISTUNG oder TAUGLICHKEIT FÜR EINEN BESTIMMTEN ZWECK.

Besuche [http://opensource.org/licenses/MIT](http://opensource.org/licenses/MIT) um mehr über die MIT Lizenz zu erfahren.

## TODO
- geojson to postgresql import
- update Funktion für die Daten im Docker Container
- Shell Script für Bezirksgrenzen.geojson download
- Funktion für Berechnung der Shortest Paths in die eine Liste an id's übergeben werden kann und die fertige Catchment Tabelle retour kommt.

## QUELLEN
- [Docker](https://www.docker.com/)
- [pgRouting Image](ttps://hub.docker.com/r/starefossen/pgrouting/)
- [postgreSQL](https://www.postgresql.org/)
- [osm2pgsql @ OSM Wiki](http://wiki.openstreetmap.org/wiki/Osm2pgsql)
- [PostGIS](http://postgis.org/)
- [PostGIS Tutorial](http://workshops.boundlessgeo.com/postgis-intro/)
- [pgRouting](http://pgrouting.org/)
- [osm2pgrouting](http://pgrouting.org/docs/tools/osm2pgrouting.html)
- [osm2pgRouting @ OSM Wiki](http://wiki.openstreetmap.org/wiki/Osm2pgrouting)
- [pgRouting Workshop](http://workshop.pgrouting.org/index.html)
- [pgRouting Tutorial](http://workshops.boundlessgeo.com/tutorial-routing/)
- [A Beginners Guide to pgrouting](https://anitagraser.com/2011/02/07/a-beginners-guide-to-pgrouting/)
- [An osm2po Quickstart](https://anitagraser.com/2011/12/15/an-osm2po-quickstart/)
- [OSM Advanced](https://anitagraser.com/2011/12/18/osm2po-part-2-pgrouting-on-osm-the-easy-way/)
- [Drive Time Isochrones – An Example Using Finnish Airports](https://anitagraser.com/2011/02/12/drive-time-isochrones/)
- [Creating Catchment Areas with pgRouting and QGIS](https://anitagraser.com/2011/02/09/creating-catchment-areas-with-pgrouting-and-qgis/)
- [OGR](http://wiki.openstreetmap.org/wiki/OGR)
- [Strassengraphen GIP von Anita Graser](https://anitagraser.com/projects/ogdwien-notizen/)

## AUFBAU
- [README.md](README.md): Übersicht zu Repo
