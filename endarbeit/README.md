Endarbeit VU Angewandte GIS Grundlagen
==============================

Dies ist die Endarbeit der Lehrveranstaltung VU Angewandte GIS-Grundlagen.

Die Aufgabenstellung wurde selber ausgearbeítet. Ziel war es, den kompletten Datenverarbeitungsprozess durch die Erstellung, Nutzung und Analyse von räumlichen Informationen anhand von OpenStreetMap zu erlernen. Dadurch sollte der gesellschaftliche Nutzen von Open Data und Open Source durch den kompletten Daten-Zyklus sichtbar gemacht und auch selber gleich dazu beigetragen werden.

Bei der Aufgabe wurden anhand von Open Government Data und OpenStreetMap Daten der kompletten Lebenszyklus von Daten, von der Erstellung bis zur wissenschaftlichen Analyse dargelegt. Zu Beginn wurde mit Map your Hood eine OpenStreetMap Mapping-Party organisiert, wo Daten zu Kebab-Restaurants im Bezirk Gries in Graz im Gelände gesammelt und dann in die OpenStreetMap für alle zugänglich eingetragen wurde. Die so vervollständigten Daten wurden in einem weiteren Schritt runtergeladen und für eine Erreichbarkeits-Analyse für Fussgänger_innen in einer räumlichen Datenbank verwaltet. Die finalen Daten wurden dann in ein Docker-Image geladen und im Web geteilt, was ein einfaches nutzen und erweitern der Daten für andere ermöglicht.

Zentrale Arbeitsgrundlage waren die Tutorials von [Anita Graser](https://anitagraser.com/) sowie Workshop-Materialien. Vielen Dank!
- [pgRouting Workshop](http://workshop.pgrouting.org/index.html)
- [pgRouting Tutorial](http://workshops.boundlessgeo.com/tutorial-routing/)
- [A Beginners Guide to pgrouting](https://anitagraser.com/2011/02/07/a-beginners-guide-to-pgrouting/)
- [Drive Time Isochrones – An Example Using Finnish Airports](https://anitagraser.com/2011/02/12/drive-time-isochrones/)
- [Creating Catchment Areas with pgRouting and QGIS](https://anitagraser.com/2011/02/09/creating-catchment-areas-with-pgrouting-and-qgis/)

Die unten angeführten Data-Processing Schritte (Punkt 3-5) können selber nachgemacht werden, da die finalen Daten aber im Docker-Image enthalten ist können auch gleich diese für die Visualisierung im Punkt 6  genutzt werden.

**Inhalt**

1. [Map your Hood](#1-map-your-hood)
2. [Setup](#2-setup)
3. [Daten aufbereiten](#3-daten-aufbereiten)
	- Bezirksgrenze Gries importieren
	- OpenStreetMap Daten (Kebab-Restaurants) importieren
	- OpenStreetMap Daten (Strassengraph) importieren
4. [Erreichbarkeit berechnen](#4-erreichbarkeit-berechnen)
5. [Docker Image erstellen](#5-docker-image-erstellen)
6. [Karte erstellen](#6-karte-erstellen)
	- Layer laden
	- Catchment Areas darstellen
	- Karte exportieren
- [Sonstiges](#sonstiges)
- [Quellen](#quellen)
- [UrheberInnenrecht](#urheber_innenrecht)
- [Beitragen](#beitragen)
- [Todo](#todo)
- [Repository](#repository)

**Verwendete Software**

- [Ubuntu 14.04](http://ubuntu.com/)
- [postgreSQL](www.postgresql.org/) (9.3.15, Datenbank) mit [PostGIS](http://postgis.org/) (2.1, für Spatial Queries und Geometrien als Datentyp) und [pgRouting](http://pgrouting.org/) (2.2.3, für Routing Funktionen) Erweiterung. Dies ist somit eine komplette Open Source Datenbank um mit räumlichen Daten zu arbeiten.
- [pgAdmin 3](https://www.pgadmin.org/) (1.18.1)
- [QGIS](http://www.qgis.org/de/site/) (2.8.1): GIS Desktop Anwendung für Analysen und Erstellung von Karten.
- [osm2pgsql](http://learnosm.org/en/osm-data/osm2pgsql/) (0.82.0): Import Tool von OpenStreetMap Daten in postgreSQL Datenbanken mit PostGIS Erweiterung.
- [osm2pgRouting](http://pgrouting.org/docs/tools/osm2pgrouting.html) (2.1): Import Tool von OpenStreetMap Daten als routing-fähige Topology in eine postgreSQL Datenbank mit pgRouting Erweiterung.
- [Docker](https://www.docker.com/) (1.13.1) und  [pgRouting Image](ttps://hub.docker.com/r/starefossen/pgrouting/): Docker ist eine Art Virtual Machine und ermöglicht somit das Verwenden eines Container (Image) auf verschiedenen Betriebssystemen. Als Grundlage wurde ein bereits vorhandenes Image mit pgRouting genutzt.
- [git](http://git-scm.com/) (1.9.1): Versionierungs-Software. Wurde zum Versionieren des GitHub Repos und deren Inhalte genutzt.
- [wget](https://www.gnu.org/software/wget/) (1.15)

**Verwendete Daten**

Es wurden offene Daten aus folgenden Datenportalen verwendet:
- OpenStreetMap via [Overpass API](http://overpass-api.de/): OpenStreetMap-Mitwirkende unter [Open Data Commons Open Database Lizenz (ODbL)](https://opendatacommons.org/licenses/odbl/).
- [Orthophotos Graz](http://data.graz.gv.at/daten/package/orthophotos): Stadt Graz unter [Creative Commons Namensnennung 3.0 Österreich Lizenz](https://creativecommons.org/licenses/by/3.0/at/).
- [Bezirksgrenzen Graz](https://github.com/species/OGD-Graz-Daten/blob/master/Bezirksgrenzen/Bezirksgrenzen.geojson): Stadt Graz unter [Creative Commons Namensnennung 3.0 Österreich Lizenz](https://creativecommons.org/licenses/by/3.0/at/).

**Urheber_innenrecht**

Alle Werke (Daten, Quellcode, Content) die in dieser Aufgabe verwendet oder erzeugt wurden, stehen unter einer freien Lizenz. Mehr Infos gibt es unter dem Punkt Urheber_innenrecht.

## 1. MAP YOUR HOOD
**[Map your Hood!](https://github.com/skasberger/map-your-hood)**: OpenStreetMap Mapping Party im Bezirk Gries in Graz. Es nahmen 13 Personen teil.

Map your hood! war eine OpenStreetMap Mapping Party im Bezirk Gries in Graz, bei der es um die Erfassung von ortsbezogenen Daten im Gelände und dem zugänglich-machen dieser für alle ging. OpenStreetMap ist die Wikipedia für geographische Informationen, und steht somit allen frei zum Beitragen und Nutzen zur Verfügung. Zuerst gab es eine Einführung in den OpenStreetMap Editor [JOSM](https://josm.openstreetmap.de/), bevor es ins Gelände raus gegangen ist und die kulinarische Szene von Gries erfasst wurde. Die gesammelten Daten wurden am Ende gemeinsam in die OpenStreetMap eingetragen bevor die Ergebnisse auf [openstreetmap.org](http://openstreetmap.org/) betrachtet wurden. Somit ging es um die Erfassung von ortsbezogenen Daten im Gelände und dem zugänglich-machen dieser.

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

**OpenStreetMap Daten runterladen**

Die OpenStreetMap-Daten werden via [overpass turbo API](https://overpass-turbo.eu/) runtergeladen. Diese API ermöglicht komfortables Speichern von OpenStreetMap Daten innerhalb einer definierten Boundary Box. Diese kann nach eigenen Bedürfnissen angepasst und so andere "Rechtecke" runtergeladen werden.

```bash
BBOX="15.402231216430662,47.034800577106964,15.452785491943358,47.07509065142444"
wget --progress=dot:mega -O "$ROOT_DIRECTORY/data/raw/osm/bbox.osm" "http://www.overpass-api.de/api/xapi?*[bbox=${BBOX}][@meta]"
```

![Download OpenStreetMap Daten](/assets/images/endarbeit/download-osm-data.png)

Bild: Download der OpenStreetMap Boundary Box (bbox).

**OpenStreetMap Daten in postgres Datenbank importieren**
Dazu das Tool [osm2pgsql](http://learnosm.org/en/osm-data/osm2pgsql/) installieren und die [Style File](apps/osm2pgsql/default.style) so anpassen. Dabei müssen folgende Attribute angegeben werden, damit diese zu den einzelnen Punkten importiert werden.
- name
- addr:housenumber
- addr:street
- cuisine
- amenity

![bbox Strassengraph](/assets/images/endarbeit/bbox-strassengraph.png)

Bild: Strassengraph der bbox nach pgRouting Import

Die OpenStreetMap Daten werden dann mittels osm2pgsql in die PostGIS Datenbank importiert. osm2pgsql legt dazu ein neues Schema mit dem Namen ```osm_raw``` an, die Tabellen haben alle den Präfix ```bbox_```.
```bash
osm2pgsql -H localhost -p bbox -s -E 3857 -U postgres -d vugis -S $ROOT_DIRECTORY/apps/osm2pgsql/default.style /my-data/science/academia/vu-gis-grundlagen/endarbeit/data/raw/osm/bbox.osm
```

Die importierten Tabellen müssen nun noch in das OSM Schema ```public``` in postgreSQL rüber kopiert werden.
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

Die OpenStreetMap Daten in pgRouting in das Schema ```osm_pg_raw``` importieren:
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

Damit die später folgenden Berechnungen nicht unnötig viel Zeit benötigen, wird der Graph auf seine Mindestgröße reduziert, nämlich auf den Bezirk Gries. Dazu wird zuerst ein Buffer mit 200m um die Bezirksgrenze von Gries gelegt, um direkt darum liegende Wege des Strassengraphen noch in die Berechnung miteinzubeziehen. Dies wird in PostGIS gemacht.

```sql
CREATE TABLE data.bezirksgrenze_gries_buffer AS SELECT ST_Buffer(geom,200) AS geom FROM data.bezirksgrenze_gries;
```

![alt text](/assets/images/endarbeit/gries-buffer.png)
Bild: Der erweiterte Buffer-Bereich rund um die Bezirksgrenze von Gries.

Damit später Spatial Queries ([ST_WITHIN](http://postgis.net/docs/ST_Within.html)) gemacht werden können, muss noch das Koordinaten-System der ```ways``` und der ```ways_vertices_pgr``` Tabelle transformiert ([ST_TRANSFORM](http://postgis.net/docs/ST_Transform.html)) werden (SRID = 31256). Dazu werden die bestehenden Tabellen kopiert und die Geometrie danach abgeändert - alles in PostGIS.

```sql
CREATE TABLE osm.bbox_ways_31256 AS SELECT * FROM osm_pg_raw.ways;

ALTER TABLE osm.bbox_ways_31256
  ALTER COLUMN the_geom
  TYPE Geometry(LineString, 31256)
  USING ST_Transform(the_geom, 31256);

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

Damit später der Shortest Path Algorithmus verwendet werden kann, müssen die Spalten der Tabelle noch in die korrekten Datentypen konvertiert werden.
```sql
CREATE TABLE osm.gries_ways_clean_31256 AS SELECT gid::integer AS id, class_id, length, length_m, name, source::integer, target::integer, x1, y1, x2, y2, cost::double precision, reverse_cost, cost_s, reverse_cost_s, rule, one_way, maxspeed_forward, maxspeed_backward, osm_id, source_osm, target_osm, priority, the_geom FROM osm.gries_ways_31256;
```

Danach ist der Strassengraph für die Netzwerk-Analyse fertig und sieht so aus.

![Gries OpenStreetMap Ways](/assets/images/endarbeit/gries-ways.png)
Bild: Darstellung des bereinigten Strassengraphen ```gries_ways_vertices_pgr_31256``` und ```gries_ways_clean_31256```.

## 4. ERREICHBARKEIT BERECHNEN

Um die Catchment-Area berechnen zu können, muss die kürzeste Distanz zwischen allen Punkten im Strassengraphen berechnet werden.

Dazu muss zuerst zu jedem Kebab-Restaurant-Punkt der am nächsten gelegene Knoten des Strassengraphen ([ST_DISTANCE](http://postgis.net/docs/ST_Distance.html)) gefunden werden, da die beiden Layer/Tabellen nicht miteinander verbunden oder ident sind. Die gefundene osm_id wird dann als ```osm.gries_kebab_31256.nearest_node_osm_id``` abgespeichert.
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

Die gefundenen Knoten werden nun als Start-Knoten für den [Dijkstra Shortest Path Algorithmus](http://docs.pgrouting.org/2.2/en/src/dijkstra/doc/pgr_dijkstra.html#pgr-dijkstra) übergeben. Dieser berechnet nun für jedes Kebab-Restaurant den kürzesten Weg zu sämtlichen Punkten (Knoten) im Strassengraphen. Um das Ergebnis verwenden zu können, wird als Cost gleich die Länge in Metern übergeben.

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

Als Ergebnis bekommt man für jeden Punkt im Strassengraphen einen Werte je Kebab-Restaurant (8 Stück), welcher die Distanz (Cost) in Metern zwischen dem einzelnen Punkt und dem jeweiligen Kebab-Restaurant darstellt. Wir wollen aber nur die Distanz eines Punktes zum nächstgelegenen Kebab-Restaurant, weshalb zum Schluss noch der kleinste Wert für jeden Punkt im Strassengraph ausgewählt werden muss.
```sql
CREATE TABLE osm.catchment_kebab_final_31256 AS
  SELECT id, the_geom, min(cost) AS cost FROM osm.catchment_kebab_31256
  GROUP BY id, the_geom;
```

Das Ergebnis sieht dann wie folgt aus:

![Strassengraph-Knoten mit Kosten](/assets/images/endarbeit/costs-points.png)

Bild: Die Punkte des Strassengraphen mit der minimalen Distance dargestellt (abgestuft in 5, 10, 20, 30 und 60 Minuten).

![Strassengraph-Knoten mit Kosten](/assets/images/endarbeit/costs-points-groß.png)

Bild: Die Knoten des Strassengraphen mit der minimalen Distance in Nahaufnahme (abgestuft in 5, 10, 20, 30 und 60 Minuten).

## 5. DOCKER IMAGE ERSTELLEN

### Datenbank aus postgreSQL exportieren
Die gesamte Datenbank in eine SQL Datei speichern, welche danach einfach importiert werden kann
```bash
pg_dump -h localhost -U postgres -d vugis > $ROOT_DIRECTORY/data/sql/vugis.sql
```

SQL-Datei in Docker-Ordner kopieren.
```bash
cp /$ROOT_DIRECTORY/data/sql/vugis.sql $ROOT_DIRECTORY/data/docker
```

### a) manuelles Importieren der SQL-File in Docker

Wie findet man die IP raus mittels DOCKER_IMAGE_ID?
```bash
docker exec $DOCKER_IMAGE_ID cat /etc/hosts
```

Bitte hier das eigene Passwort und die passende Image-ID einsetzen.

```bash
POSTGRES_PASSWORD=YOUR_PASSWORD
DOCKER_IMAGE_ID=YOUR_IMAGE_ID
```

Als Basis für das Docker-Image wurde das Image [starefossen/pgrouting](https://hub.docker.com/r/starefossen/pgrouting/) verwendet. 

Starten des Docker-Images als daemon:
```bash
docker run --name pgrouting-daemon -e POSTGRES_PASSWORD=$POSTGRES_PASSWORD -d starefossen/pgrouting
```

Nun muss man in die Shell des Docker-Containers, damit man von dort die die Datenbank erstellen und dann die SQL-Datei importieren kann. 
```bash
docker run -it --link pgrouting-daemon:postgres -v $ROOT_DIRECTORY/data/docker:/data --rm postgres /bin/bash
```

Wenn dies funktioniert hat, muss in der Shell ```root@<ID>``` stehen, das einem sagt, dass man als root-User im Docker-Container angemeldet ist. Mit den root-Rechten kann nun die ```vugis```-Datenbank angelegt werden (im Docker-Container):
```bash
psql -U postgres -h postgres -c "CREATE DATABASE vugis"
```

Danach importieren wir den Dumps in die angelegte Datenbank (im Docker-Container).
```bash
psql -U postgres -h postgres vugis < /data/vugis.sql
```

### b) Erstelltes Docker-Image verwenden
Wir haben das Docker-Image auf DockerHub zur freien Nutzung zur Verfügung gestellt. Einfach runter laden, den Container starten und schon kannst du mit den Daten lokal arbeiten. 

**[cheeseman/vugis](https://hub.docker.com/r/cheeseman/vugis/)**

## 6. KARTE ERSTELLEN

### Layer laden
QGIS öffnen und 31256 als CRS einstellen.

![QGIS](/assets/images/endarbeit/qgis-leer.png)

Bild: QGIS nach dem Starten.

Dann Verbindung mit dem Docker Container aufbauen.

![Docker Verbindung](/assets/images/endarbeit/docker-verbindung.png)

Bild: Verbindung zur postgreSQL Datenbank des Docker Containers aufbauen.

[Orthophotos Graz](http://data.graz.gv.at/daten/package/orthophotos) als Hintergrund laden. Dazu ist eine WMS-Verbindung mit dem Server der Stadt Graz aufzubauen.

![Orthophotos Graz](/assets/images/endarbeit/orthophoto-graz.png)

Bild: Importieren des Orthophoto Graz als Hintergrund-Layer.

Danach die Bezirksgrenze Gries aus der postgreSQL-Datenbank importieren und mit dicken Aussengrenzen sowie transparenter Füllung darstellen

![Bezirksgrenze Gries](/assets/images/endarbeit/gries.png)

Bild: Die Bezirksgrenze von Gries.

Den Strassengraph aus der Datenbank importieren und passend anzeigen.

![Gries OpenStreetMap Ways](/assets/images/endarbeit/gries-ways.png)

Bild: Strassengraph aus postgreSQL.

![Gries OpenStreetMap Ways Nahaufnahme](/assets/images/endarbeit/gries-ways-groß.png)

Bild: Strassengraph in Nahaufnahme.

Die Kebab Restaurants importieren und passend anzeigen.

![Kebab Restaurants Gries](/assets/images/endarbeit/kebab-restaurants.png)

Bild: Kebab Restaurants in Gries.

### Catchment Areas darstellen

Um die Erreichbarkeit für Fußgänger darstellen zu können, muss zuerst die Distanz die in den jeweiligen Zeiten gegangen werden errechnet werden. Bei einer Durchschnittsgeschwindigkeit von 4km/h bedeutet dies:

| Minuten | Meter |
| --- | --- |
| 5 | 333 |
| 10 | 666 |
| 20 | 1333 |
| 30 | 2000 |
| 60 | 4000 |

Zur Erstellung der Catchment-Areas wird das Interpolations-Plugin verwendet. Dieses muss wie folgt eingestellt werden:

- Vector layers: catchment_kebab_final_31256
- Interpolation attribute: cost
- "Add" button klicken um das Attribut hinzuzufügen.
- Output file angeben.

![Einstellungen Interpolations-Plugin](/assets/images/endarbeit/interpolation-settings.png)

Bild: Einstellung Interpolations-Plugin.

Das Ergebnis muss in etwa so aussehen:

![Catchment Area](/assets/images/endarbeit/catchment-tiff.png)

Bild: Catchment Areas durch Interpolations-Plugin

Als nächster Schritt wollen wir Isobaren entlang der oben definierten Abständen errechnen. Dazu wird das Contour-Plugin mit folgenden Einstellungen verwendet:

- Vector Layer: catchment_kebab_final_31256
- Data field: cost
- Number 5
- Werte: siehe Tabelle oben (Meter bis zum nächsten Kebab-Restaurant).
- Output Layer name: catchment_kebab_final_31256_cost

![Einstellungen Contour-Plugin](/assets/images/endarbeit/catchment-contour-settings.png)

Bild: Einstellung der Kontur-Linien

Nach ausführen der Contour-Funktion sollten diskrete Linien zu den Metern bis zum nächsten Kebab-Restaurant erscheinen.

![Catchment Area mit Contour-Linien](/assets/images/endarbeit/catchment-countours.png)

Bild: Catchment Areas mit Kontur-Linien.

Zu den Catchment-Areas wird am Ende noch der Strassengraph hinzugefügt um die Erreichbarkeit besser analyiseren zu können und das Styling der Kontouren angepasst.
![Finale Karte](/assets/images/endarbeit/final-map.png)

Bild: Catchment Areas mit Kontur-Linien, Strassengraph und Styling.

### Karte exportieren
Zum Schluss wird noch eine Druckzusammenstellung mit der finalen Karte erstellt..

![Druckzusammenstellung](/assets/images/endarbeit/map-composer.png)

Bild: Druckzusammenstellung

Diese wird dann als SVG, PNG und PDF exportiert:
![alt text](/assets/images/endarbeit/map.jpg)
Bild: Finale Karte Erreichbarkeits-Analyse.

Die höher aufgelösten Orginal-Karten sind hier zu finden: [Orginal Karten](/endarbeit/images/)


## SONSTIGES

### Import mit osm2po
[osm2po](http://osm2po.de/) installieren ([A osm2po Quickstart](https://anitagraser.com/2011/12/15/an-osm2po-quickstart/)). Ist besonders für große Imports nützlich.

- demo.sh kopieren, umbenennen (graz.sh) und umschreiben
- graz.sh ausführen

```
sh graz.sh
```
Die dadurch erstellte SQL Datei kann nun mit psql ganz einfach importiert werden.

```
psql -h localhost -U postgres -d vugis -q -f "/my-data/apps/osm2po-5.1.0/osm2po_graz/osm2po_graz_2po_4pgr.sql"
```

Shortest Path Algorithmus testen (Node 1679 zu 4239):
```
CREATE TABLE route_1679_4239 AS SELECT seq, node, edge, a.cost, a.agg_cost, b.geom_way FROM pgr_dijkstra('SELECT id, source, target, cost, reverse_cost FROM osm2po_graz_2po_4pgr', 1679, 4239 ) a LEFT JOIN osm2po_graz_2po_4pgr b ON (a.edge = b.id);
```

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

Spatial Query in QGIS:
```
"cuisine" = 'kebab' OR "cuisine" = 'kebab,pizza' OR "cuisine" = 'kebab;pizza' OR "cuisine" = 'kebab;pizza;ice_cream' OR "cuisine" = 'kebap, pizza' OR "cuisine" = 'kebap,pizza' OR "cuisine" = 'kebap;pizza'
```


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

## URHEBER_INNENRECHT

Alle Werke sind offen lizenziert unter der [Creative Commons Namensnennung 4.0](http://creativecommons.org/licenses/by/4.0/) Lizenz, soweit nicht anders erwähnt.

Der Quellcode ist freie Software: du kannst ihn verbreiten und/oder abändern unter den Bedingungen der MIT Lizenz.

Der Quellcode wird zur Verfügung gestellt, unter der Hoffnung dass er nützlich ist, aber OHNE JEGLICHE HAFTUNG; auch ohne der implizierten Haftung der GEWÄHRLEISTUNG oder TAUGLICHKEIT FÜR EINEN BESTIMMTEN ZWECK.

Besuche [http://opensource.org/licenses/MIT](http://opensource.org/licenses/MIT) um mehr über die MIT Lizenz zu erfahren.

## Verwendete Daten
Es wurden nur offene Daten verwendet:
- OpenStreetMap via [Overpass API](http://overpass-api.de/): OpenStreetMap-Mitwirkende unter [Open Data Commons Open Database Lizenz (ODbL)](https://opendatacommons.org/licenses/odbl/).
- [Orthophotos Graz](http://data.graz.gv.at/daten/package/orthophotos): Stadt Graz unter [Creative Commons Namensnennung 3.0 Österreich Lizenz](https://creativecommons.org/licenses/by/3.0/at/).
- [Bezirksgrenzen Graz](https://github.com/species/OGD-Graz-Daten/blob/master/Bezirksgrenzen/Bezirksgrenzen.geojson): Stadt Graz unter [Creative Commons Namensnennung 3.0 Österreich Lizenz](https://creativecommons.org/licenses/by/3.0/at/).

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

Wenn du was machen willst, reiche einen [pull request](https://github.com/skasberger/vu-angewandte-gis-grundlagen/pulls) ein.

### Issue erstellen

Wir verwenden den [GitHub issue tracker](https://github.com/skasberger/vu-angewandte-gis-grundlagen/issues) um Bugs und Features zu verwalten. Bevor du einen Bug Report oder einen Feature Request absendest, bitte nachsehen ob dies nicht bereits gemeldet worden ist. Wenn du einen Bug meldest füge bitte einen Screenshot hinzu um das Problem zu beschreiben.

## TODO
- geojson to postgresql import
- update Funktion für die Daten im Docker Container
- Shell Script für Bezirksgrenzen.geojson download
- Funktion für Berechnung der Shortest Paths in die eine Liste an id's übergeben werden kann und die fertige Catchment Tabelle retour kommt.

## REPOSITORY
- [README.md](README.md): Übersicht zu Repo und Tutorial
- [images/](images): Karten
- [apps/osm2pgsql/default.style](apps/osm2pgsql/default.style): Style File für pgrouting-Import
