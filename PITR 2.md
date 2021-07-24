# **Point In Time Recovery (PITR)**

---

## Task 2: Base Backup using `pg_dump`

1. `pg_dump` into the specific location for 4 tables.
2. Another 3 tables need to be created. 
    - related `WAL` files to be created
    - `time` need to be noted.
3. Current DB drop and relaod the earlier pg_dump using `pg_restore` [.sql]
    `or` 
3. Current DB uninstall and new db install. And reload the earlier pg_dump 
4. PITR 

---

## Connect postgres in CMD windows

```sql
psql -U userName
```
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






---

- **Sample**

```cmd
testpitr1 : '2021-07-23 21:48:11.281186+06'					
bkp-01
testpitr2 : '2021-07-23 22:15:09.980697+06'
testpitr3 : '2021-07-23 22:22:05.974484+06' 
testpitr4 : '2021-07-23 22:36:03.418849+06' truncate='2021-07-23 22:55:28.175244+06'
```

---
