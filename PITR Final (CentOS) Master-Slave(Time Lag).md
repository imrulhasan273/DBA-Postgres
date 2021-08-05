# **PITR Final on (CentOS) Master-Slave(Time Lag)**

---

## Prerequisite

- `Streaming Replication` and `Delay Replication` is necessary to doing this task

- Remove all the previous files `archive_wal_dir` before start. [Clean Set Up]

- First Set Up `Streaming Replication` in **Slave Server** without time delay.

---

## MASTER [Query]

---

```sql
postgres# create schema pitr; --
postgres# create table pitr.PITR1 as select * from pg_class, pg_description; --2289009	--
postgres# create table pitr.PITR2 as select * from pg_class, pg_description; --2303052	--
postgres# create table pitr.PITR3 as select * from pg_class, pg_description; --2317095	--
```

- As of now `Database` is Synced between **Master** and **Slave**
	- Because `Streaming Replication` in **Slave Server** without time delay.

## Slave Server Configuration to add `Delay Time`

- `postgresql.conf`

```shell
synchronous_commit = remote_apply
recovery_min_apply_delay = 7200s		# '60s' or '12h' or '1min' or '1d'
```

- Restart Required

```shell
root $ sudo systemctl restart postgresql-13	# Restart to take effect the changes
```

---

## MASTER [Query]

---

### Do Some Query

```sql
postgres# create table pitr.PITR4 as select * from pg_class, pg_description; --2317095	
postgres# insert into pitr.PITR3 select * from pg_class, pg_description;	 --4810095 [2*1] ==> NOTE TIME TO PITR
postgres# drop table pitr.PITR2;					
```			

> This query will be take effect after 2 hours 

### Permission to files in wal dir `archive_wal_dir`

```shell
root $ chmod 777 *		# Warning: Please do this inside archive_wal_dir directory
```

---

## Before PITR

---

### Stop the Slave server

```shell
root $ sudo systemctl stop postgresql-13	# Stop the server to work
```

### Rename the **Standby Mode** `indicator` file

```shell
root $ mv standby.signal standby.signal.bkp
```

---

## PITR

---

### Slave Server Configuration to `Restore` wal files

- prostgresql.conf

```shell
restore_command = 'cp  /mnt/archive_wal_dir/%f %p'
recovery_target_time = '2021-08-03 14:49:23.923197+06'
# BELOW 2 OPTIONAL::ALREADY SET BY DEFAULT
recovery_target_action = 'pause'	# 'promote'			|		'pause'::select pg_wal_replay_resume();
recovery_target_inclusive = 'false'
```

### Create the `Recovery Mode` indicator file

```shell
root $ touch recovery.signal
```

> This file Will be vanished after completing the `recovery`

### Start the Server again

```shell
root $ sudo systemctl start postgresql-13
```

### Need to resume the DB from Recovering mode

- `psql`

```sql
postgres# SELECT pg_wal_replay_resume(); 	--If recovery_target_action == 'pause'
```

---











