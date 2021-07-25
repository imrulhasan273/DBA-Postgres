# **Point In Time Recovery (PITR)**

---

---

## Flow 

1. TK Backup
2. Loading backup [`dump` file]
3. Taking Base Backup
    - `data` folder copy to remote location
    - Run `traditional` commands of `basebackup`
4. Some Operation [DDL/DML] 
    - Note the `time` after operations
5. Drop the **point(4)** `tables` 
6. Restore the point(4) table using PITR
    - Remove the `data` folder
    - Take back the `Base Backup` from remote location to where the data folder exists
    - PITR using `recovery_target_time` and `recovery_command`

---

## Some DB Commands

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" start
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" stop
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" restart
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" status
```

## Backup

- A `dump` file is at `D:\pg_dump`. Which is out **backup** file of schema.

## Loading backup [`dump` file]

- Restore using `psql`

```sql
cmd> psql -h localhost -p 5432 -U postgres -d postgres -f D:\pg_dump\pitr.sql
```

## Taking `Base Backup`

- Traditional Methods to create base backups

```sql
postgres=# select pg_start_backup('Full Backup - Testing');
postgres=# select pg_stop_backup(); 
```

- Need to copy folder of the directory `"(D:\InstalledPostgreSQL\data)"` as backup into remote location as `data` (same name as it is).


## Some Operation [DDL/DML] 

- table `pitr.testPITR3`

```sql
postgres=# create table pitr.testPITR3 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; 
```

- table `pitr.testPITR4`

```sql
postgres=# create table pitr.testPITR4 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; 
```

- Note the `time` after operations

## Stop the DB server

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" stop
```

## Need to rename `"(D:\InstalledPostgreSQL\data)"` to `"(D:\InstalledPostgreSQL\data_bkp)"`

- Let's assume data folder is vanished by mistake. Instead of delete we just renamed the folder as data_bkp.


## Need to take back copy of `"(D:\InstalledPostgreSQL\data)"` from the remote location

- Get back `data` folder from `bkp` folder

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

## Need to create signal file recovery.signal in `"(D:\InstalledPostgreSQL\data)"` (Base Backup)

- this recovery.signal is just an empty file which indicates that data should start recovering when db starts.
- Its not mandatory for v10.

## Need to restart the db. Automatically restoring will be started

```sql
cmd> pg_ctl -D "D:\InstalledPostgreSQL\data" start
```

## Need to resume the DB from Recovering mode

```sql
postgres=# SELECT pg_wal_replay_resume(); --Move to normal mode
```

- [v13]

> data\recovery.signalfile will be removed

- [v10]

> data\recovery.conf file will be renamed as recovery.done

## Now DB is perfect!! If needed Base Backup can be taken

