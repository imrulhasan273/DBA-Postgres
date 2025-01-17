# **Point In Time Recovery (PITR)**

---

## Flow

1. Some Operation 
2. Taking `Base Back Up` and Copy `"D:\InstalledPostgreSQL\data"` Folder in Remote Location. 
3. Some Operation
4. Delete the `data` folder in `"D:\InstalledPostgreSQL\data"`
5. Then Take back the `data` folder which was taken as backup in remote location.
6. Run PITR

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
- Some Operations

```sql
postgres=# create table pitr.testPITR1 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; -- 2021-07-23 21:48:11.281186+06
```

5. Need to take the **Base Backup** 

- Traditional Methods to create base backups

```sql
postgres=# select pg_start_backup('Full Backup - Testing');
postgres=# select pg_stop_backup(); 
```

> The main advantage of this `Traditional Methods` instead of `pg_basebackup` is that there is no need to open a database connection, and no need to configure the XLOG streaming infrastructure on the source server. 

> Another main advantage is that you can make use of features such as ZFS snapshots or similar means that can help reduce dramatically the amount of I/O needed to create an initial backup.

6. Need to copy directory `"(D:\InstalledPostgreSQL\data)"` as backup into remote location as `data` (same name as it is).

> This backup is called **Base Backup**

7. Need to perform `DML/DDL` activity. Timestamp needs to be captured

```SQL
postgres=# create table pitr.testPITR2 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; -- 2021-07-23 22:15:09.980697+06
```

- Truncate `pitr.testPITR2` table 

```sql
postgres=# select count
postgres=# (1) from pitr.testPITR2;  --millions of rows 

postgres=# truncate table pitr.testPITR2;

postgres=# select count
postgres=# (1) from pitr.testPITR2;  --0 rows

postgres=# select * from current_timestamp; --2021-07-23 22:55:28.175244+06
```

> Here we `truncate` data of `testpitr2` table by mistake. We need to go to the state(time) when `testpitr2` table have data. 


8. If any incident occurred (Or Manually need to stop the db)

- Now stop the db

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" stop
```

## **Restoring Process**

9. Before restoring process

> for safety purpose, archiving directory `"D:\ihPgDataPITR"` can be taken as backup for further same PITR recovering.

> May be we need to do the recovery process again if the recovery is unsuccessfull. We can not use the `"D:\ihPgDataPITR"` twich as the folder will be modified. That's why we just backup the folder for future use if needed.

10. Need to rename `"(D:\InstalledPostgreSQL\data)"` to `"(D:\InstalledPostgreSQL\data_bkp)"`

> Let's assume `data` folder is vanished by mistake. Instead of **delete** we just **renamed** the folder as `data_bkp`.

11. Need to take back copy of `"(D:\InstalledPostgreSQL\data)"` from the remote location `[Point: 6]`

> The **base backup** data folder was named as `data` in remote location. Just **copy** and **past** the folder in `"(D:\InstalledPostgreSQL\)"` location.

12. Configure or create recovery file in `Base Backup` folder.

- `[v13]` Need to configure the restoring `(D:\InstalledPostgreSQL\data\postgresql.conf)`

```cmd
restore_command = 'copy "D:\\ihPgDataPITR\\%f" "%p"'
recovery_target_time = '2021-07-23 22:36:03.418849+06'
```

- `[v10]` Need to create recovery configure file `(D:\InstalledPostgreSQL\data\recovery.conf)`

```cmd
restore_command = 'copy "D:\\ihPgDataPITR\\%f" "%p"'
recovery_target_time = '2021-07-23 22:36:03.418849+06'
```

> Here, We have full data on time `'2021-07-23 22:36:03.418849+06'`. We truncated our `testpitr2` table on `'2021-07-23 22:55:28.175244+06'`. So recovery_target_time should be the time when we have full data thats `'2021-07-23 22:36:03.418849+06'`.

13. Need to create signal file `recovery.signal` in `"(D:\InstalledPostgreSQL\data)"` (Base Backup)

> this `recovery.signal` is just an empty file which indicates that data should start recovering when db starts.

> Its not mandatory for `v10`.

14. Need to restart the db. Automatically restoring will be started

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" restart
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" start
```

> After `start` the **db** again the `recovery` will start. 

15. Need to resume the **DB** from `Recovering mode` 

```sql
postgres=# SELECT pg_wal_replay_resume(); --Move to normal mode
```

- `[v13] `

> `data\recovery.signal`file will be removed 	

- `[v10]`

> `data\recovery.conf` file will be renamed as `recovery.done`


16. Now DB is perfect!! If needed **Base Backup** can be taken

- We can now safely delete the folder `data_bkp` from `D:\InstalledPostgreSQL\` location.

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" start
```

---

- **Sample**

```cmd
testpitr1 : '2021-07-23 21:48:11.281186+06'					
bkp-01
testpitr2 : '2021-07-23 22:36:03.418849+06' truncate='2021-07-23 22:55:28.175244+06'
```

---


## Questionaries

- 56:: Crashing during WAL writing
- 103::More sophisticated positioning in the XLOG



