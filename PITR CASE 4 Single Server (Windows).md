# **Point In Time Recovery (PITR)**

---

## Flow 

1. Table-1 Creation
    - `time` need to be noted
2. Taking **Base Backup** `1`
    - Run `Traditional` commands of `basebackup`
    - `data` folder copy to remote location ==> `(data1)`
3. Table-2 Creation 
    - `time` need to be noted
4. Taking **Base Backup** `2`
    - Run `Traditional` commands of `basebackup`
    - `data` folder copy to remote location ==> `(data2)`
5. Table-3 Creation 
    - PITR `time` need to be noted after operations where data exists
6. Table-3 Drop  **||** Table-2 and Table-3 drop
    - `time` need to be noted
7. Restore at point (5) from `(data1)` of **Base Backup**

---

---

1. Table-1 Creation

- table `pitr.testPITR1`

```sql
postgres=# create table pitr.testPITR1 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; 
```

> `time` need to be noted

2. Taking **Base Backup** `1`

- Run `Traditional` commands of `basebackup`

```sql
postgres=# select pg_start_backup('Full Backup - Testing');
postgres=# select pg_stop_backup(); 
```

- `data` folder copy to remote location ==> `(data1)`

> Need to copy folder of the directory `"(D:\InstalledPostgreSQL\data)"` as backup into remote location as `data1` 


3. Table-2 Creation

- table `pitr.testPITR2`

```sql
postgres=# create table pitr.testPITR2 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; 
```

> `time` need to be noted

4. Taking **Base Backup** `2`

- Run `Traditional` commands of `basebackup`

```sql
postgres=# select pg_start_backup('Full Backup - Testing');
postgres=# select pg_stop_backup(); 
```

- `data` folder copy to remote location ==> `(data2)`

> Need to copy folder of the directory `"(D:\InstalledPostgreSQL\data)"` as backup into remote location as `data2` 


5. Table-3 Creation

- table `pitr.testPITR3`

```sql
postgres=# create table pitr.testPITR3 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; 
```

> `time` need to be noted


6. Table-3 Drop  **||** Table-2 and Table-3 drop
    

```sql
postgres=# drop table pitr.testpitr3;
postgres=# select * from current_timestamp; 
```
> `or` 

```sql
postgres=# drop table pitr.testpitr2;
postgres=# drop table pitr.testpitr3;
postgres=# select * from current_timestamp; 
```

7. Restore at point (5) from `(data1)` of **Base Backup**


- Stop the DB server

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" stop
```

- Need to rename `"(D:\InstalledPostgreSQL\data)"` to `"(D:\InstalledPostgreSQL\data_bkp)"`

- Need to take back copy of `data1` from the remote location

- rename `data1` to `data`

## Configure or create recovery file in Base Backup folder.

- [v13] Need to configure the restoring (D:\InstalledPostgreSQL\data\postgresql.conf)

```sql
restore_command = 'copy "D:\\ihPgDataPITR\\%f" "%p"'
recovery_target_time = '2021-07-23 22:36:03.418849+06'
```

- [v10] Need to create recovery configure file (D:\InstalledPostgreSQL\data\recovery.conf)

```sql
restore_command = 'copy "D:\\ihPgDataPITR\\%f" "%p"'
recovery_target_time = '2021-07-23 22:36:03.418849+06'
```

## Need to create signal file `recovery.signal` in `"(D:\InstalledPostgreSQL\data)"` (Base Backup)

- this `recovery.signal` is just an empty file which indicates that data should start recovering when db starts.
- Its not mandatory for v10.

## Need to restart the db. Automatically restoring will be started

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" restart
```

## Need to resume the DB from Recovering mode

```sql
postgres=# SELECT pg_wal_replay_resume(); --Move to normal mode
```

- [v13]

> `data\recovery.signal` file will be removed

- [v10]

> `data\recovery.conf` file will be renamed as recovery.done

## Now DB is perfect!! If needed Base Backup can be taken

---





