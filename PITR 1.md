# **Point In Time Recovery (PITR)**

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

5. Need to take the **base backup** after (if needed) few DDL/DML operation

```sql
cmd> psql -U postgres
```

- Some Operations

```sql
postgres=# create table testPITR1 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; -- 2021-07-23 21:48:11.281186+06
```

- Traditional Methods to create base backups

```sql
postgres=# select pg_start_backup('Full Backup - Testing');
postgres=# select pg_stop_backup(); 
```

> The main advantage of this `Traditional Methods` instead of `pg_basebackup` is that there is no need to open a database connection, and no need to configure the XLOG streaming infrastructure on the source server. 

> Another main advantage is that you can make use of features such as ZFS snapshots or similar means that can help reduce dramatically the amount of I/O needed to create an initial backup.

6. Need to copy directory `"(D:\InstalledPostgreSQL\data)"` as backup into remote location as `ihdata`.

> This backup is called **Base Backup**

7. Need to perform `DML/DDL` activity. Timestamp needs to be captured

```SQL
postgres=# create table testPITR2 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; -- 2021-07-23 22:15:09.980697+06
```

```SQL
postgres=# create table testPITR3 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; -- 2021-07-23 22:22:05.974484+06
```

```SQL
postgres=# create table testPITR4 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; -- 2021-07-23 22:36:03.418849+06
```

- Truncate `testPITR4` table 

```sql
postgres=# select count
postgres=# (1) from testpitr4;  --millions of rows 

postgres=# truncate table testpitr4;

postgres=# select count
postgres=# (1) from testpitr4;  --0 rows

postgres=# select * from current_timestamp; --2021-07-23 22:55:28.175244+06
```

> Here we `truncate` data of `testpitr4` table by mistake. We need to go to the state(time) when `testpitr4` table have data. 


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

> The **base backup** data folder was named as `ihdata`. Just **copy** and **past** the folder in `"(D:\InstalledPostgreSQL\)"` location.

12. Configure or create recovery file in `Base Backup` folder.

- `[v13]` Need to configure the restoring `(D:\InstalledPostgreSQL\ihData\postgresql.conf)`

```cmd
restore_command = 'copy "D:\\ihPgDataPITR\\%f" "%p"'
recovery_target_time = '2021-07-23 22:36:03.418849+06'
```

- `[v10]` Need to create recovery configure file `(D:\InstalledPostgreSQL\ihData\recovery.conf)`

```cmd
restore_command = 'copy "D:\\ihPgDataPITR\\%f" "%p"'
recovery_target_time = '2021-07-23 22:36:03.418849+06'
```

> Here, We have full data on time `'2021-07-23 22:36:03.418849+06'`. We truncated our `testpitr4` table on `'2021-07-23 22:55:28.175244+06'`. So recovery_target_time should be the time when we have full data thats `'2021-07-23 22:36:03.418849+06'`.

13. Need to create signal file `recovery.signal` in `"(D:\InstalledPostgreSQL\ihdata)"` (Base Backup)

> this `recovery.signal` is just an empty file which indicates that data should start recovering when db starts.

14. Need to restart the db. Automatically restoring will be started

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\ihdata" restart
cmd> pg_ctl -D "D:\InstalledPostgreSQL\ihdata" start
```

> After `start` the **db** again the `recovery` will start. 

15. Need to resume the **DB** from `Recovering mode` 

```sql
postgres=# SELECT pg_wal_replay_resume(); --Move to normal mode
```

- `[v13] `

> `ihdata\recovery.signal`file will be removed 	

- `[v10]`

> `ihdata\recovery.conf` file will be renamed as `recovery.done`


16. Now DB is perfect!! If needed **Base Backup** can be taken

> Now we can renamed the folder `ihdata` as `data` after confirmed.

> Also start the db again for `data`

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" start
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


## Questionaries

- 56:: Crashing during WAL writing
- 103::More sophisticated positioning in the XLOG



