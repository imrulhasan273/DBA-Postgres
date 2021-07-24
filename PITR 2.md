# **Point In Time Recovery (PITR)**

---

## Task 2: Base Backup using `pg_dump`

1. `pg_dump` into the specific location for 2 tables.
2. Another 2 tables need to be created. 
    - related `WAL` files to be created
    - `time` need to be noted.
3. Schema drop and relaod the earlier pg_dump using `pg_restore` [.sql]
    `or` 
3. Current DB uninstall and new db install. And reload the earlier pg_dump 
4. PITR 

---


## **Initial**

1. Install Postgres `10` or `13` on directory `"D:\InstalledPostgreSQL"`
    - Need to give full permission on directory `"D:\InstalledPostgreSQL"`

---

## **Archiving Process**

2. Need to configure the archiving `"D:\InstalledPostgreSQL\data\postgresql.conf"`

```sql
--Windows
archive_mode = on
archive_command = 'IF NOT EXIST "D:\\ihPgDataPITR\\%f" echo not_exist && copy "%p" "D:\\ihPgDataPITR\\%f"'  
```

3. Need to create directory `"D:\\ihPgDataPITR"` for WAL file archiving 

- Some important DB command

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" start
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" stop
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" restart
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" status
```

> **cmd** from `D:\InstalledPostgreSQL\bin` if not works.

4. Need to restart the DB after configuration

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" restart
```

### Some DDL and DML Operations before Base Backup

- Connect to postgresql as user `postgres`

```sql
cmd> psql -U postgres
```

- Listing `DB's`

```sql
cmd> \l
```

- Connect to the DB `postgres`

```sql
cmd> \c postgres --You are now connected to database "postgres" as user "postgres".
```

- Creating schema `pitr`

```sql
CREATE SCHEMA pitr;
```

- Listing all Tables in Schema `pitr`

```sql
\dt pitr.*
```

- `Create table 1`

```sql
postgres=# create table pitr.testPITR1 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; -- 2021-07-24 16:17:45.11939+06
```

- `Create table 2`

```sql
postgres=# create table pitr.testPITR2 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; -- 2021-07-24 16:19:08.496295+06
```

---

- Traditional Methods to create `Base Backups`

```sql
postgres=# select pg_start_backup('Full Backup - Testing');
postgres=# select pg_stop_backup(); 
```

> The main advantage of this Traditional Methods instead of pg_basebackup is that there is no need to open a database connection, and no need to configure the XLOG streaming infrastructure on the source server.

> Another main advantage is that you can make use of features such as ZFS snapshots or similar means that can help reduce dramatically the amount of I/O needed to create an initial backup.

---

5. **BACKUP** using `pg_dump` with plain `sql`

```sql
cmd> pg_dump -h localhost -p 5432 -U postgres -d postgres -n pitr >> D:\pg_dump\pitr.sql
```

### Some DDL and DML Operations before DROP DB

- `Create table 3`

```sql
postgres=# create table pitr.testPITR3 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; -- 2021-07-24 18:29:11.558104+06
```

-  `Create table 4`

```sql
postgres=# create table pitr.testPITR4 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; -- 2021-07-24 18:31:34.151855+06
```

- Drop Schema `pitr`

```sql
postgres=# DROP SCHEMA pitr CASCADE;
```


## Restoring Process

6. **Restore** using `psql`

```sql
cmd> psql -h localhost -p 5432 -U postgres -d postgres -f D:\pg_dump\pitr.sql
```

7. If any incident occurred (Or Manually need to stop the db)

- Now stop the db

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" stop
```

8. Configure or create recovery file in `D:\InstalledPostgreSQL\data` folder.

- `[v13]` Need to configure the restoring `(D:\InstalledPostgreSQL\Data\postgresql.conf)`

```cmd
restore_command = 'copy "D:\\ihPgDataPITR\\%f" "%p"'
recovery_target_time = '2021-07-24 18:31:34.151855+06'
```

- `[v10]` Need to create recovery configure file `(D:\InstalledPostgreSQL\Data\recovery.conf)`

```cmd
restore_command = 'copy "D:\\ihPgDataPITR\\%f" "%p"'
recovery_target_time = '2021-07-24 18:31:34.151855+06'
```

9. Need to create signal file `recovery.signal` in `"(D:\InstalledPostgreSQL\data)"` 

> this `recovery.signal` is just an empty file which indicates that data should start recovering when db starts.

10. Need to restart the db. Automatically restoring will be started

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" restart
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" start
```

> After `start` the **db** again the `recovery` will start. 

11. Need to resume the **DB** from `Recovering mode` 

```sql
postgres=# SELECT pg_wal_replay_resume(); --Move to normal mode
```

- `[v13] `

> `data\recovery.signal`file will be removed 	

- `[v10]`

> `data\recovery.conf` file will be renamed as `recovery.done`


12. Now DB is perfect!! If needed **Base Backup** can be taken

> Now we can renamed the folder `ihdata` as `data` after confirmed.

> Also start the db again for `data`

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" start
```

---






