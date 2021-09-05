# Neo4j DBA ops

* [Preamble](#preamble)
* [Operations](#operations)
  * [Running Cypher commands from the shell](#running-cypher-commands-from-the-shell)
  * [Backup and restore databases](#backup-and-restore-databases)
  * [Copy a database and optionaly apply filters](#copy-a-database-and-optionaly-apply-filters)
  * [Dump and load databases](#dump-and-load-databases)
  * [Set up Browser views](#set-up-browser-views)
  * [Set up Bloom views](#set-up-bloom-views)
* [Administration](#administration)
  * [Manage databases](#manage-databases)
  * [Clear database](#clear-database)
  * [User and role management](#user-and-role-management)
  * [Consistency check](#consistency-check)

## Preamble

To ensure the appropriate file permissions and limits, `neo4j-admin` utility should be invoked as the `neo4j` user. You should precede every invocation of `neo4j-admin` with the following `sudo` command:

```bash
$ sudo -u neo4j neo4j-admin ‹command›
```

## Operations

### Running Cypher commands from the shell

Neo4j Operations Manual › Tools › Cypher Shell  
https://neo4j.com/docs/operations-manual/current/tools/cypher-shell/

```bash
$ cypher-shell -u ‹username› [-p ‹password›] [--database ‹name›] [-a protocol://host:port] [cypher command]
```

Examples:

```bash
$ cypher-shell -uneo4j -a bolt+ssc://‹hostname›:7687
```

or just after the install of Neo4j, before any SSL Policy was setup:

```bash
$ cypher-shell
username: neo4j
password: *****

Connected to Neo4j 4.1.0 at neo4j://localhost:7687 as user neo4j.
Type :help for a list of available commands or :exit to exit the shell.
Note that Cypher queries must end with a semicolon.
neo4j@neo4j> SHOW DATABASES;
…
neo4j@neo4j> :use ‹databaseName›
neo4j@truc> :source ‹path/to/my-script.cypher›
```

### Backup and restore databases

```bash
$ neo4j-admin backup --database=neo4j --backup-dir=data/backups/neo4j/
$ neo4j-admin restore --database neo4j --from data/backups/neo4j/
```

### Copy a database and optionaly apply filters

Neo4j Operations Manual › Tools › Copy a database  
https://neo4j.com/docs/operations-manual/current/tools/copy/

```bash
$ neo4j-admin copy (--from-database=<database> | --from-path=<path>) [--force]
                   [--verbose] [--from-pagecache=<size>] [--from-path-tx=<path>]
                   --to-database=<database> [--to-format=<format>]
                   [--to-pagecache=<size>] [--delete-nodes-with-labels=<label>[,
                   <label>...]]... [--skip-labels=<label>[,<label>...]]...
                   [--skip-properties=<property>[,<property>...]]...
                   [--skip-relationships=<relationship>[,<relationship>...]]...
```

Don't forget that after a copy, even if a new database exists on the server, it is _not automatically picked up_ by Neo4j! To start the new database copy, you have to insert it into Neo4j with the following Cypher query; Neo4j will then detect the copied database and begin to use that:

```cypher
CREATE DATABASE copy;
```

### Dump and load databases

Neo4j Operations Manual › Tools › Dump and load databases  
https://neo4j.com/docs/operations-manual/current/tools/dump-load/

```bash
$ neo4j-admin dump --database=neo4j   --to=data/dumps/20200618-db-neo4j.dump
$ neo4j-admin load --database=neo4j --from=data/dumps/20200618-db-neo4j.dump [--force]
```

Load and dump operations are are meant to move a database from one system to another, but they are a bit of impractical for taking snapshots of _online_ databases:

* The database needs to be stopped before dumping it, then restarted, using `STOP/START DATABASE`
* If the database doesn’t exist prior to loading, it must be created, using `CREATE DATABASE`
* When loading database dumps, if the database already exists, you must drop it first, using `DROP DATABASE`.

In case of «Not a valid Neo4j archive» exception, check this article:  
[Neo4j KB › neo4j-admin load causes "Not a valid Neo4j archive"](https://neo4j.com/developer/kb/neo4j-admin-load-causes-not-a-valid-neo4j-archive/)

### Set up Browser views

Browser views rely on a `.grass` file, available in the `views/browser` folder. Simply drag and drop that file into a browser cell to get default viewing config set up. You can then modify and export again. 

The Browser interface also allows for creating favorite `.cql` files that can be quickly called from the interface. These can be saved by clicking on the star in the top right of a cell. They can then be exported once saved. Some of those exports - outdated - are in the `views/browser` folder as well. 

### Set up Bloom views

Bloom allows to set up views on the data, through "perspectives". Some perspectives are saved in the `/views/bloom` folder.

## Administration

### Manage databases

Neo4j Operations Manual › Manage Databases › Administration and configuration  
https://neo4j.com/docs/operations-manual/current/manage-databases/configuration/#manage-databases-administration

|Command|Description|
|---|---|
|`:dbs`|Show all available databases (similar to `SHOW DATABASES`)|
|`:use ‹name›`|Updates which database to use as target for Cypher.|
|`:sysinfo`|Show current stats on your database.|
|`CREATE DATABASE ‹name›`|Create and start a new database.|
|`DROP DATABASE ‹name›`|Drop (remove) an existing database.|
|`START DATABASE ‹name›`| Start a database that has been stopped.|
|`STOP DATABASE ‹name›`| Shut down a database.|
|`SHOW DATABASE ‹name›`| Show the status of a specific database.|
|`SHOW DATABASES`|Show the name and status of all the databases.|
|`SHOW DEFAULT DATABASE`| Show the name and status of the default database.|

All of the above commands are executed as Cypher commands, and the database name is subject to the standard Cypher restrictions on valid identifiers. In particular, the `-` (dash) and `.` (dot) characters are not legal in Cypher variables, and therefore names with dashes must be enclosed within back-ticks. For example, CREATE DATABASE \`main-db\`. Database names are the only identifier for which dots don’t need to be escaped. For example, `main.db` is a valid database name.

### Clear database

To clean all nodes/relationships of a database, without dropping it, in case the statement `MATCH (n) DETACH DELETE n` would overflow the memory:

```cypher
$ :use ‹dbname›
$ CALL apoc.periodic.iterate("MATCH (n) RETURN n", "DETACH DELETE n", {batchSize:5000})
  YIELD batches, total RETURN batches, total
```

### User and role management

#### Changer user/pwd du ‹super user› du DBMS Neo4j

```cypher
$ :use system
$ ALTER USER neo4j SET PASSWORD "‹mynewpass›";
```

#### Créer un rôle

```cypher
$ :use system
$ CREATE ROLE ‹businessRole›;
$ GRANT MATCH {*} ON GRAPH ctracing TO ‹businessRole›;
$ GRANT WRITE ON GRAPH ctracing TO ‹businessRole›;
$ GRANT ACCESS ON DATABASE ctracing TO ‹businessRole›;
$ SHOW ROLE ‹businessRole› PRIVILEGES;
```

#### Consulter tous les rôles

```cypher
$ :use system
$ SHOW ROLES
$ SHOW ROLE editor PRIVILEGES;
```

#### Créer nouvel utilisateur

```cypher
$ use :system
$ CREATE USER ‹businessUser01›
  SET PASSWORD '‹businessUser01Pwd›' CHANGE NOT REQUIRED
  SET STATUS ACTIVE;
$ GRANT ROLE ‹businessRole› TO ‹businessUser01›;
```

#### Consulter tous les utilisateurs et leurs rôles

```cypher
$ :use system
$ SHOW USERS;
```

#### Voir aussi

* [Neo4j Cypher Manual › Administration › Security › User and role management](https://neo4j.com/docs/cypher-manual/current/administration/security/users-and-roles/#administration-security-users-show-current)
* [Neo4j Operations Manual › Authentication and authorization › Built-in roles](https://neo4j.com/docs/operations-manual/current/authentication-authorization/built-in-roles/)
* [Neo4j Operations Manual › Configuration › Password and user recovery](https://neo4j.com/docs/operations-manual/current/configuration/password-and-user-recovery/)

### Consistency check

```bash
$ cypher-shell -uneo4j -a bolt+ssc://‹hostname›:7687 --database "system" "STOP DATABASE neo4j"
password: *******
0 rows available after 810 ms, consumed after another 0 ms

$ neo4j-admin check-consistency --database "neo4j"
2020-12-15 12:26:44.261+0000 WARN [o.n.i.p.PageCache] The dbms.memory.pagecache.size setting has not been configured. It is recommended that this setting is always explicitly configured, to ensure the system has a balanced configuration. Until then, a computed heuristic value of 3221051392 bytes will be used instead. Run `neo4j-admin memrec` for memory configuration suggestions.
2020-12-15 12:26:44.970+0000 INFO [o.n.k.i.s.f.RecordFormatSelector] Selected RecordFormat:StandardV4_0[SF4.0.0] record format from store /var/lib/neo4j/data/databases/neo4j
2020-12-15 12:26:44.971+0000 INFO [o.n.k.i.s.f.RecordFormatSelector] Format not configured for store /var/lib/neo4j/data/databases/neo4j. Selected format from the store files: RecordFormat:StandardV4_0[SF4.0.0]
Index structure consistency check
....................  10%
....................   …
.................... 100%
Consistency check
....................  10%
....................   …
.................... 100%

$ cypher-shell -uneo4j -a bolt+ssc://‹hostname›:7687 --database "system" "START DATABASE neo4j"
password: *******
0 rows available after 9 ms, consumed after another 0 ms
```