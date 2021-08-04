# PITR 

---

- Remove all the files `archive_wal_dir` before start

---

## MASTER

---

```sql
create schema pitr; --
create table pitr.PITR1 as select * from pg_class, pg_description; --2289009	--
create table pitr.PITR2 as select * from pg_class, pg_description; --2303052	--
create table pitr.PITR3 as select * from pg_class, pg_description; --2317095	--
```

- Synced in **`Master`** and **`Slave`**



---- Add delay time to Slave [restart required] -- [sudo systemctl restart postgresql-13]
-----------------------------------------------
synchronous_commit = remote_apply
recovery_min_apply_delay = 1800s

--------MASTER
--------------
create table pitr.PITR4 as select * from pg_class, pg_description; --2317095	
insert into pitr.PITR3 select * from pg_class, pg_description;	   --480000+    --[T] '2021-08-04 08:40:09.825436+06'
drop table pitr.PITR2;								
---Delayed 30 Minutes from Master in Slave


----- Standby Mode to Normal Mode (Slave Server)
------------------------------------------------
1. stop server
2. mv standby.signal standby.signal.bkp

----PITR : 
----------
1. chmod 777 archive_wal_dir/*	--permission to this dir
3. Configure the prostgresql.conf file
	restore_command = 'cp  /mnt/archive_wal_dir/%f %p'
	recovery_target_time = '2021-08-03 14:49:23.923197+06' --insert
4. Create recovery.signal file
6. Start the server [PITR will start auto]	[sudo systemctl start postgresql-13]
7. SELECT pg_wal_replay_resume(); --in slave












