# oereb-db
Docker image providing a PostGIS database with OEREB tables

Das Dockerimage dient während der Entwicklung verschiedenen Zwecken:

- OEREB-DB: Das Image enthält die identische Struktur, d.h. `stage`- und `live`-Schema.
- Edit-DB: Es werden sowohl das originale Edit-Schema eines Themas angelegt, wie auch das Staging-Schema (mit Endung "_oereb"). Das Edit-Schema ist sehr individuell. Aus diesem Grund muss jedes Schema separat im Code behandelt werden.

Ebenfalls wird das DDL-SQL der OEREB-DB für die spätere Integration auf dem GDI-DB-Cluster.

`initdb-user.sh` muss mit dem Code synchron gehalten werden, damit die Rechte an den Schemen und Tabellen korrekt gesetzt sind.

## Usage

```
docker run --rm --name oerebdb -p 54323:5432 -e POSTGRES_PASSWORD=mysecretpassword -e POSTGRES_DB=oereb sogis/oereb-db:2
```

```
mkdir -m 0777 ~/pgdata-oereb
docker run --rm --name oerebdb -p 54323:5432 -v ~/pgdata-oereb:/var/lib/postgresql/data:delegated -e POSTGRES_PASSWORD=mysecretpassword -e POSTGRES_DB=oereb -e PG_READ_PWD=dmluser -e PG_WRITE_PWD=ddluser -e GRETL_PG_WRITE_PWD=gretl sogis/oereb-db:2
```

## Develop

```
jbang edit create_schema_sql.java
```

Auf Apple Silicon ist _vschromium_ noch nicht verfügbar. Aus diesem Grund muss als Editor _VS Code_ verwendet werden. Damit der Aufruf via Console funktioniert, muss in `.zshrc` der PATH angepasst werden:

```
export PATH="$PATH:/Users/stefan/apps/vscode/Visual Studio Code.app/Contents/Resources/app/bin"
```

Der der Aufruf zum Editieren ist wie folgt:

```
jbang edit --open=code create_schema_sql.java
```

Code ausführen:

```
jbang create_schema_sql.java
```

Falls _jbang_ nicht installiert ist:

```
curl -Ls https://sh.jbang.dev | bash -s - create_schema_sql.java
```

## Build 

Die Pipeline erstellt das Image. Das Herstellen der verschiedenen SQL-Dateien (für das Image selber und für die GDI) kann auch lokal gemacht werden (siehe Develop).