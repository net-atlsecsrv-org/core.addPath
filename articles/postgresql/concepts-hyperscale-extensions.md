---
title: Extensions – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Describes the ability to extend the functionality of your database by using extensions in Azure Database for PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
---
# PostgreSQL extensions in Azure Database for PostgreSQL – Hyperscale (Citus)

PostgreSQL provides the ability to extend the functionality of your database by using extensions. Extensions allow for bundling multiple related SQL objects together in a single package that can be loaded or removed from your database with a single command. After being loaded in the database, extensions can function like built-in features. For more information on PostgreSQL extensions, see [Package related objects into an extension](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## Use PostgreSQL extensions

PostgreSQL extensions must be installed in your database before you can use them. To install a particular extension, run the [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html) command from the psql tool to load the packaged objects into your database.

Azure Database for PostgreSQL - Hyperscale (Citus) currently supports a subset of key extensions as listed here. Extensions other than the ones listed aren't supported. You can't create your own extension with Azure Database for PostgreSQL.

## Extensions supported by Azure Database for PostgreSQL

The following tables list the standard PostgreSQL extensions that are currently supported by Azure Database for PostgreSQL. This information is also available by running `SELECT * FROM pg_available_extensions;`.

The versions of each extension installed in a server group sometimes differ based on the version of PostgreSQL (11, 12, or 13). The tables list extension versions per database version.

### Citus extension

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citus](https://github.com/citusdata/citus) | Citus distributed database. | 9.5-1 | 9.5-1 | 10.0-2 |

### Data types extensions

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Provides a case-insensitive character string type. | 1.5 | 1.6 | 1.6 |
> | [cube](https://www.postgresql.org/docs/current/static/cube.html) | Provides a data type for multidimensional cubes. | 1.4 | 1.4 | 1.4 |
> | [hll](https://github.com/citusdata/postgresql-hll) | Provides a HyperLogLog data structure. | 2.14 | 2.15 | 2.15 |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Provides a data type for storing sets of key-value pairs. | 1.5 | 1.6 | 1.7 |
> | [isn](https://www.postgresql.org/docs/current/static/isn.html) | Provides data types for international product numbering standards. | 1.2 | 1.2 | 1.2 |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Large Object maintenance. | 1.1 | 1.1 | 1.1 |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Provides a data type for hierarchical tree-like structures. | 1.1 | 1.1 | 1.2 |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Data type for representing line segments or floating-point intervals. | 1.3 | 1.3 | 1.3 |
> | [tdigest](https://github.com/tvondra/tdigest) | Data type for on-line accumulation of rank-based statistics such as quantiles and trimmed means. | 1.0 | 1.0 | 1.0 |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Type for top-n JSONB. | 2.2.2 | 2.3.1 | 2.3.1 |

### Full-text search extensions

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Provides a text search dictionary template for integers. | 1.0 | 1.0 | 1.0 |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Text search dictionary template for extended synonym processing. | 1.0 | 1.0 | 1.0 |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | A text search dictionary that removes accents (diacritic signs) from lexemes. | 1.1 | 1.1 | 1.1 |

### Functions extensions

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Functions for autoincrementing fields. | 1.0 | 1.0 | 1.0 |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Provides a means to calculate great-circle distances on the surface of the Earth. | 1.1 | 1.1 | 1.1 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Provides several functions to determine similarities and distance between strings. | 1.1 | 1.1 | 1.1 |
> | [insert\_username](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Functions for tracking who changed a table. | 1.0 | 1.0 | 1.0 |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Integer aggregator and enumerator (obsolete). | 1.1 | 1.1 | 1.1 |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Provides functions and operators for manipulating null-free arrays of integers. | 1.2 | 1.2 | 1.3 |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Functions for tracking last modification time. | 1.0 | 1.0 | 1.0 |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Manages partitioned tables by time or ID. | 4.1 | 4.4.1 | 4.4.1 |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Provides functions and operators for determining the similarity of alphanumeric text based on trigram matching. | 1.4 | 1.4 | 1.5 |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Provides cryptographic functions. | 1.3 | 1.3 | 1.3 |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Functions for implementing referential integrity (obsolete). | 1.0 | 1.0 | 1.0 |
> | session\_analytics | Functions for querying hstore arrays. | | | |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Provides functions that manipulate whole tables, including crosstab. | 1.0 | 1.0 | 1.0 |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Triggered change notifications. | 1.0 | 1.0 | 1.0 |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Functions for implementing time travel. | 1.0 | | |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Generates universally unique identifiers (UUIDs). | 1.1 | 1.1 | 1.1 |

### Index types extensions

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Bloom access method - signature file-based index. | 1.0 | 1.0 | 1.0 |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Provides sample GIN operator classes that implement B-tree-like behavior for certain data types. | 1.3 | 1.3 | 1.3 |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Provides GiST index operator classes that implement B-tree. | 1.5 | 1.5 | 1.5 |

### Language extensions

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL loadable procedural language. | 1.0 | 1.0 | 1.0 |

### Miscellaneous extensions

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Administrative functions for PostgreSQL. | 2.0 | 2.0 | 2.1 |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Functions for verifying relation integrity. | 1.1 | 1.2 | 1.2 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | A module that supports connections to other PostgreSQL databases from within a database session. See the "dblink and postgres_fdw" section for information about this extension. | 1.2 | 1.2 | 1.2 |
> | [file\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Foreign-data wrapper for flat file access. | 1.0 | 1.0 | 1.0 |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Inspect the contents of database pages at a low level. | 1.7 | 1.7 | 1.8 |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Provides a means for examining what's happening in the shared buffer cache in real time. | 1.3 | 1.3 | 1.3 |
> | [pg\_cron](https://github.com/citusdata/pg_cron) | Job scheduler for PostgreSQL. | 1.1 | 1.3 | 1.3 |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Examine the free space map (FSM). | 1.2 | 1.2 | 1.2 |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Provides a way to load relation data into the buffer cache. | 1.2 | 1.2 | 1.2 |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Provides a means for tracking execution statistics of all SQL statements executed by a server. See the "pg_stat_statements" section for information about this extension. | 1.6 | 1.7 | 1.8 |
> | [pg\_visibility](https://www.postgresql.org/docs/current/pgvisibility.html) | Examine the visibility map (VM) and page-level visibility information. | 1.2 | 1.2 | 1.2 |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Provides a means for showing row-level locking information. | 1.2 | 1.2 | 1.2 |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Provides a means for showing tuple-level statistics. | 1.5 | 1.5 | 1.5 |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Foreign-data wrapper used to access data stored in external PostgreSQL servers. See the "dblink and postgres_fdw" section for information about this extension.| 1.0 | 1.0 | 1.0 |
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Information about TLS/SSL certificates. | 1.2 | 1.2 | 1.2 |
> | [tsm\_system\_rows](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABLESAMPLE method, which accepts number of rows as a limit. | 1.0 | 1.0 | 1.0 |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE method, which accepts time in milliseconds as a limit. | 1.0 | 1.0 | 1.0 |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath querying and XSLT. | 1.1 | 1.1 | 1.1 |


### PostGIS extensions

> [!div class="mx-tableFixed"]
> | **Extension** | **Description** | **PG 11** | **PG 12** | **PG 13** |
> |---|---|---|---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Spatial and geographic objects for PostgreSQL. | 2.5.1 | 3.0.3 | 3.0.3 |
> | address\_standardizer, address\_standardizer\_data\_us | Used to parse an address into constituent elements. Used to support geocoding address normalization step. | 2.5.1 | 3.0.3 | 3.0.3 |
> | postgis\_sfcgal | PostGIS SFCGAL functions. | 2.5.1 | 3.0.3 | 3.0.3 |
> | postgis\_tiger\_geocoder | PostGIS tiger geocoder and reverse geocoder. | 2.5.1 | 3.0.3 | 3.0.3 |
> | postgis\_topology | PostGIS topology spatial types and functions. | 2.5.1 | 3.0.3 | 3.0.3 |


## pg_stat_statements
The [pg\_stat\_statements extension](https://www.postgresql.org/docs/current/pgstatstatements.html) is preloaded on every Azure Database for PostgreSQL server to provide you with a means of tracking execution statistics of SQL statements.

The setting `pg_stat_statements.track` controls what statements are counted by the extension. It defaults to `top`, which means that all statements issued directly by clients are tracked. The two other tracking levels are `none` and `all`. This setting is configurable as a server parameter through the [Azure portal](./howto-configure-server-parameters-using-portal.md) or the [Azure CLI](./howto-configure-server-parameters-using-cli.md).

There's a tradeoff between the query execution information pg_stat_statements provides and the effect on server performance as it logs each SQL statement. If you aren't actively using the pg_stat_statements extension, we recommend that you set `pg_stat_statements.track` to `none`. Some third-party monitoring services might rely on pg_stat_statements to deliver query performance insights, so confirm whether this is the case for you or not.

## dblink and postgres_fdw

You can use dblink and postgres\_fdw to connect from one PostgreSQL server to
another, or to another database in the same server.  The receiving server needs
to allow connections from the sending server through its firewall.  To use
these extensions to connect between Azure Database for PostgreSQL servers or
Hyperscale (Citus) server groups, set **Allow Azure services and resources to
access this server group (or server)** to ON.  You also need to turn this
setting ON if you want to use the extensions to loop back to the same server.
The **Allow Azure services and resources to access this server group** setting
can be found in the Azure portal page for the Hyperscale (Citus) server group
under **Networking**.  Currently, outbound connections from Azure Database for
PostgreSQL Single server and Hyperscale (Citus) aren't supported, except for
connections to other Azure Database for PostgreSQL servers and Hyperscale
(Citus) server groups.
