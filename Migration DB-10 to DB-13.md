# **Migration**

---

## Useful Links

---

- [upgrading-and-updating-postgresql](https://www.cybertec-postgresql.com/en/upgrading-and-updating-postgresql/)
- [how-to-upgrade-from-postgresql-11-to-12/](https://www.postgresql.r2schools.com/how-to-upgrade-from-postgresql-11-to-12/)
- [how-to-upgrade-from-postgresql-11-to-12/VIDEO](https://www.youtube.com/watch?v=u1sTwvCKmIU)
- [how-to-upgrade-postgresql-database-from-10-to-12-without-losing-data-for-openpro](https://stackoverflow.com/questions/60409585/how-to-upgrade-postgresql-database-from-10-to-12-without-losing-data-for-openpro)
- [epas_upgrade_guide_07_migration](https://www.enterprisedb.com/docs/epas/latest/epas_upgrade_guide/07_migration/)
- [migrating-the-data-from-postgresql-9-x-to-10-x](https://docs.bmc.com/docs/btco113/migrating-the-data-from-postgresql-9-x-to-10-x-800581922.html)
- [pgupgrade](https://www.postgresql.org/docs/13/pgupgrade.html)
- [upgrading](https://www.postgresql.org/docs/12/upgrading.html)
- [migrating-to-postgresql-version-13-incompatibilities-you-should-be-aware-of](https://www.percona.com/blog/2020/07/28/migrating-to-postgresql-version-13-incompatibilities-you-should-be-aware-of/)

---

# **upgrading-and-updating-postgresql**

---

## Two Kinds of Migrations

- **Option 1:** Updating PostgreSQL
- **Option 2:** Upgrading PostgreSQL

---

### Option 1: Updating PostgreSQL [Not Our Case]

---

- **PostgreSQL 13.0 to PostgreSQL 13.1	[Update is enough]**


- **Way:**

```shell
pg_dump             # Dump / reload not required
```

```shell
Tooling	            # pg_dump			
Task	            # Minor release update				
Reloading data      # Not needed
Downtime needed     # Close to zero					
```

- Step 1: **install** the `new binaries` and **restart** the database. `[no need to reload the data.]`

---

### Option 2: Upgrading PostgreSQL	  [Our Case]

---

- **PostgreSQL 9.5, 9.6, 10, 11 or 12 to PostgreSQL 13.1	[upgrade is needed.]**

- **Ways:**

```shell
pg_dump             # Dump / reload	    :: might take a lot of time     				  :: time increased with db size
pg_upgrade          # binary level	  	:: cp data from old dir to new dir				  :: time increased with db size
pg_upgrade –link    # In-place upgrades :: new_data and old_data dir ==> same filesystem  :: ->0 downtime, amount of data != limiting factor 
```

- **Waly 1:**

```shell
Tooling	            # pg_upgrade			
Task	            # Major release update			
Reloading data      # Copy is needed
Downtime needed     # Downtime is needed			
```		

- **Way 2:**

```shell
Tooling	            # pg_upgrade –link			
Task	            # Major release update			
Reloading data      # Only hardlinks
Downtime needed     # Close to zero			
```		

> Note: 

1. pg_upgrade is never destructive.	
    - if things go wrong, you can always delete the new data directory and start from scratch.
2. `pg_upgrade` is only going to work in case the encodings of the old and the new database instance match. Otherwise, it will fail
3. **pg_upgrade:** Basically, we need 4 pieces of information here: 
    - The old data directory
    - The new data directory
    - the path of the old binaries:
    - the path of the new binaries:
    
---

## Testing

---

### Step 1::Do Some DDL/DML ::  [`postgres-10`]

- [Install Postgres](https://www.postgresql.org/download/linux/redhat/) [Any version]

```sql
postgres=# \c postgres
postgres=# create schema migration;
```

```sql
postgres=# create table migration.migration1 as select * from pg_class, pg_description ;    --create first table
postgres=# create table migration.migration2 as select * from pg_class, pg_description ;    --create second table

postgres=# CREATE INDEX idx_migration1_objoid ON migration.migration1 (objoid);
postgres=# CREATE INDEX idx_migration2_objoid ON migration.migration2 (objoid);
```

```sql
postgres=# CREATE TABLE migration.migration3 AS SELECT id AS a, id AS b, id AS c FROM generate_series(1, 50000000) AS id;
postgres=# CREATE TABLE migration.migration4 AS SELECT * FROM migration.migration3;

postgres=# CREATE INDEX idx_migration3_a ON migration.migration3 (a);
postgres=# CREATE INDEX idx_migration3_b ON migration.migration3 (b);
postgres=# CREATE INDEX idx_migration4_a ON migration.migration4 (a);
```

```sql
postgres=# CREATE TABLE migration.migration5 AS SELECT id AS a, id AS b, id AS c FROM generate_series(1, 50000000) AS id;
postgres=# CREATE TABLE migration.migration6 AS SELECT * FROM migration.migration3;

postgres=# CREATE INDEX idx_migration5_a ON migration.migration5 (a);
postgres=# CREATE INDEX idx_migration5_b ON migration.migration5 (b);
postgres=# CREATE INDEX idx_migration6_a ON migration.migration6 (a);
```

```sql
postgres=# CREATE TABLE migration.migration7 AS SELECT id AS a, id AS b, id AS c FROM generate_series(1, 50000000) AS id;
postgres=# CREATE TABLE migration.migration8 AS SELECT * FROM migration.migration3;

postgres=# CREATE INDEX idx_migration7_a ON migration.migration7 (a);
postgres=# CREATE INDEX idx_migration7_b ON migration.migration7 (b);
postgres=# CREATE INDEX idx_migration8_a ON migration.migration8 (a);
```

```sql
postgres=# CREATE TABLE migration.migration9 AS SELECT id AS a, id AS b, id AS c FROM generate_series(1, 50000000) AS id;
postgres=# CREATE TABLE migration.migration10 AS SELECT * FROM migration.migration3;

postgres=# CREATE INDEX idx_migration9_a ON migration.migration9 (a);
postgres=# CREATE INDEX idx_migration9_b ON migration.migration9 (b);
postgres=# CREATE INDEX idx_migration10_a ON migration.migration10 (a);
```

```sql
postgres=# CREATE TABLE migration.migration11 AS SELECT id AS a, id AS b, id AS c FROM generate_series(1, 50000000) AS id;
postgres=# CREATE TABLE migration.migration12 AS SELECT * FROM migration.migration3;

postgres=# CREATE INDEX idx_migration11_a ON migration.migration11 (a);
postgres=# CREATE INDEX idx_migration11_b ON migration.migration11 (b);
postgres=# CREATE INDEX idx_migration12_a ON migration.migration12 (a);
```

```sql
postgres=# CREATE TABLE migration.migration13 AS SELECT id AS a, id AS b, id AS c FROM generate_series(1, 50000000) AS id;
postgres=# CREATE TABLE migration.migration14 AS SELECT * FROM migration.migration3;

postgres=# CREATE INDEX idx_migration13_a ON migration.migration13 (a);
postgres=# CREATE INDEX idx_migration13_b ON migration.migration13 (b);
postgres=# CREATE INDEX idx_migration14_a ON migration.migration14 (a);
```

```sql
postgres=# CREATE TABLE migration.migration15 AS SELECT id AS a, id AS b, id AS c FROM generate_series(1, 50000000) AS id;
postgres=# CREATE TABLE migration.migration16 AS SELECT * FROM migration.migration3;

postgres=# CREATE INDEX idx_migration15_a ON migration.migration15 (a);
postgres=# CREATE INDEX idx_migration15_b ON migration.migration15 (b);
postgres=# CREATE INDEX idx_migration16_a ON migration.migration16 (a);
```

```sql
postgres=# CREATE TABLE migration.migration17 AS SELECT id AS a, id AS b, id AS c FROM generate_series(1, 50000000) AS id;
postgres=# CREATE TABLE migration.migration18 AS SELECT * FROM migration.migration3;

postgres=# CREATE INDEX idx_migration17_a ON migration.migration17 (a);
postgres=# CREATE INDEX idx_migration17_b ON migration.migration17 (b);
postgres=# CREATE INDEX idx_migration18_a ON migration.migration18 (a);
```

```sql
postgres=# CREATE TABLE migration.migration19 AS SELECT id AS a, id AS b, id AS c FROM generate_series(1, 50000000) AS id;
postgres=# CREATE TABLE migration.migration20 AS SELECT * FROM migration.migration3;

postgres=# CREATE INDEX idx_migration19_a ON migration.migration19 (a);
postgres=# CREATE INDEX idx_migration19_b ON migration.migration19 (b);
postgres=# CREATE INDEX idx_migration20_a ON migration.migration20 (a);
```

```sql
postgres=# CREATE TABLE migration.migration21 AS SELECT id AS a, id AS b, id AS c FROM generate_series(1, 50000000) AS id;
postgres=# CREATE TABLE migration.migration22 AS SELECT * FROM migration.migration3;

postgres=# CREATE INDEX idx_migration21_a ON migration.migration21 (a);
postgres=# CREATE INDEX idx_migration21_b ON migration.migration21 (b);
postgres=# CREATE INDEX idx_migration22_a ON migration.migration22 (a);
```

```sql
postgres=# CREATE TABLE migration.migration21 AS SELECT id AS a, id AS b, id AS c FROM generate_series(1, 50000000) AS id;
postgres=# CREATE TABLE migration.migration22 AS SELECT * FROM migration.migration3;

postgres=# CREATE INDEX idx_migration21_a ON migration.migration21 (a);
postgres=# CREATE INDEX idx_migration21_b ON migration.migration21 (b);
postgres=# CREATE INDEX idx_migration22_a ON migration.migration22 (a);
```


```SQL
postgres=# SELECT pg_size_pretty(pg_database_size('postgres'));
```

- Stop the `postgresql-10` [Older Version]

```shell
~$ sudo systemctl stop postgresql-10.service
~$ sudo systemctl status postgresql-10.service
```

---

### Step 2: `pg_upgrade` in action

---


1. **Start the DB** [`v13`]

```shell
~$ sudo systemctl start postgresql-13.service
~$ sudo systemctl status postgresql-13.service
```

2. **pg_upgrade**

- **Go to `pg-v13` bin directory**

```shell
~$ cd /usr/pgsql-13/bin/
ls
```

- **Upgrade using the following command**

```shell
~$ /usr/pgsql-13/bin/pg_upgrade \
> -b /usr/pgsql-10/bin \        # OLD Binary
> -B /usr/pgsql-13/bin \        # NEW Binary
> -d /var/lib/pgsql/10/data \   # OLD Data Directory
> -D /var/lib/pgsql/13/data \   # NEW Data Directory
> --check
```

> `DONE Successfully` uparrow and find the command and run without `--check`

```shell
~$ /usr/pgsql-13/bin/pg_upgrade -b /usr/pgsql-10/bin -B /usr/pgsql-13/bin -d /var/lib/pgsql/10/data -D /var/lib/pgsql/13/data   # ENTER
```

> `./analyze_new_cluster.sh` and `./delete_old_cluster.sh` will be created.

- **To reduce downtime, we can clean out the directory, run initdb again and add the –link option:**

```shell
> -b /usr/pgsql-10/bin \        # OLD Binary
> -B /usr/pgsql-13/bin \        # NEW Binary
> -d /var/lib/pgsql/10/data \   # OLD Data Directory
> -D /var/lib/pgsql/13/data \   # NEW Data Directory
> --link
> --check
```

---

### Step 3: **Change port numbers**

---

- `Postgresql-13`

```shell
~$ vi /var/lib/pgsql/13/data/postgresql.conf
```

```conf
port = 5432     # Uncomment the line
```

- `Postgresql-10`

```shell
~$ vi /var/lib/pgsql/10/data/postgresql.conf
```

```conf
port = 5433     # Uncomment the line
```

---

### Step 4: **Start the Server `postgresql-13`**

---

```shell
~$ sudo systemctl start postgresql-13.service
~$ sudo systemctl status postgresql-13.service
```

---

### Step 5: `psql` in `v13`

---

- Check the DB data in `postgres-v13`

---

### Step 6: Analyze new data and Remove old packages

---

```shell
~$ cd /var/lib/pgsql/
~$ ls -lrt
```

> `10`, `13`, `analyze_new_cluster.sh` and `delete_old_cluster.sh`

- Run the `analyze_new_cluster` file

```shell
postgres ~$ bash analyze_new_cluster.sh
```

- Exit 

```shell
~$ exit
```

- Installed package

```shell
~$ yum list --installed | grep postgresql   # Command not found???
```

- Now remove all the packages of old version that is `postgres-v10`

```shell
~$ sudo yum remove postgresql10*            # All the files related to postgres10 will be removed
```

- Now Installed package

```shell
~$ yum list --installed | grep postgresql   
```


- remove data dir of `pg-10`

```shell
~$ cd /var/lib/pgsql/
~$ rm -rf 10        # Inside pgsql directory
```

- Run the `delete_old_cluster` file

```shell
postgres~$ bash delete_old_cluster.sh
```

---

---

### Step 7: Connect with `psql` 13

---

- Go to `psql` prompt to query

```shell
postgres $ psql         # If Command not found ==> [Step 8]
postgres $ exit
```

### Step 8: Configure `postgresqlprofile`

- Go to `/etc/profile.d/`

```shell
~$ cd /etc/profile.d/       
~$ ls                   # postgresqlprofile.sh
```
- create `postgresqlprofile.sh` file if not exists

```shell
~$ touch postgresqlprofile.sh
```

- Open the `postgresqlprofile.sh` file

```shell
~$ vi postgresqlprofile.sh
```

- Add below lines 

```sh
export PATH=/usr/pgsql-13/bin:$PATH
```

---


