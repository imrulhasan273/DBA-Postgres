# **Some Useful Commands in Linux**

---

### Act as `root` user

```shell
sudo -i
```

### Create a file

```shell
~:$ touch test.txt
```

### Linuex Version

```shell
~:$ cat /etc/os-release
```

### To Exit from DB user

```shell
~:$ exit
```

### Enabling `Snaps` and Install gedit on `CentOS 7`

- [Instructions](https://snapcraft.io/install/gedit/centos)

### Permission Related

```shell
~:$ sudo chmod a+rwx folder	# Full Permission on Folder
```

### To Open a File using cmd

```shell
~:$ sudo gedit test.txt
```

### To open a File using `cmd` on `nano`

```shell
~:$ nano test.txt
```

> We can easily `read`, `write`, `edit` etc on that.

### To open a File using `cmd` on `vi`

```shell
~:$ vi test.txt
```

- Shift + `:/search_name` ==> Enter
    - `n` to search next  
    - `i` to insert mode ==> `ESC` to exit insert mode

- Shift + `:/q!`

- Shift + `:/qr!`

### Move a file to a folder 

```shell
~:$ mv test.txt imrul   # move file dest
```

### Copy a file to another location

```shell
~:$ cp file.txt file2.txt   # copy file1 as file2
```

### Copy a folder 

```shell
~:$ cp -R data data1
```

### Rename a directory

```shell
~:$ mv data data_bkp
```

### Count files in a folder

```shell
~:$ ls | wc -l  # 69
```

### Size of a folder

```shell
~:$ sudo du -sh pgDataPITR
```

### To delete everythong in the dir


```shell
~:$ rm *    # inside the directory
```

### Delete an empty dir

```shell
~:$ rmdir lampp
```

### Delete a non empty dir

```shell
~:$ rm -rf lampp
```

---




---

# **Postgres System Operation**

---

```shell
~:$ sudo systemctl status postgresql    # Need to be run from root user
~:$ sudo systemctl reload postgresql    # Need to be run from root user
~:$ sudo systemctl restart postgresql   # Need to be run from root user
~:$ sudo systemctl start postgresql     # Need to be run from root user
```

---

# **Some Important Task to Do**

---

### Change password of user `postgres`

```sql
ALTER USER postgres WITH PASSWORD 'postgres';  --need to be run from psql
```

### Switch between user

```shell
imrul@progoti:~$ sudo -i -u postgres   # act as postgres user
postgres@progoti:~$ 
```

### Some `psql` Operation in db

```sql
postgres@progoti:~$ psql        -- psql mode 
postgres=# \l			        -- show dbs
postgres=# \c postgres 	        -- connect db postgres
postgres=# \dt 		            -- Show tables in public schema
postgres=# create schema pitr;  -- create schema pitr
postgres=# \dt pitr.*;	        -- show all tables in pitr schema.
postgres=# drop table pitr.test;-- Drop test table in pitr schema
postgres=# drop schema pitr;	-- Drop pitr schema
postgres=# set timezone TO 'GMT+6'; -- Set time zone correctly
```

---

## **Backup and Restore**

---

### Backup db using pg_dump

```shell
~:$ pg_dump -h localhost -p 5432 -U postgres -d postgres -n pitr >> /var/lib/pgsql/10/pg_dump/pitr.sql
```

### Restore db using psql

```shell
~:$ psql -h localhost -p 5432 -U postgres -d postgres -f /var/lib/pgsql/10/pg_dump/pitr.sql
```

---

---

# **POINT IN TIME RECOVERY - LINUX**

---

## Flow

1. Table-1 Creation
    - `time` need to be noted
2. Taking **Base Backup** `1`
    - `data` folder copy to remote location ==> `(data1)`
3. Table-2 Creation 
    - `time` need to be noted
4. Taking **Base Backup** `2`
    - `data` folder copy to remote location ==> `(data2)`
5. Table-3 Creation 
    - PITR `time` need to be noted after operations where data exists
6. Table-2 and Table-3 drop
    - `time` need to be noted
7. Restore at point (5) from `(data1)` of **Base Backup**

> In some OS `main` instead of `data`. In Ubuntu it has `main`

---

## Configuration File

```shell
~:$ psql -U postgres -c 'SHOW config_file'	        # to view the postgres.conf file
~:$ less /var/lib/pgsql/10/data/postgresql.conf    # to open the file using Command 
~:$ more /var/lib/pgsql/10/data/postgresql.conf    # to open the file using Command 
```


## **Archiving**


### Step 1: Open 'postgresql.conf' using cmd

```shell
imrul@pc:/$ vi postgresql.conf  # Open the file using text editor to edit
```

### Step 2: Need to configure the archiving `postgresql.conf`

- **Local archiving**

```text
archive_mode = on
archive_command = 'test ! -f  /var/lib/pgsql/10/pgDataPITR/%f && cp %p  /var/lib/pgsql/10/pgDataPITR/%f'
```

- **Slave archiving**

```text
archive_mode = on
archive_command = 'test ! -f  /var/mnt/archive_wal_dir/%f && cp %p  /var/mnt/archive_wal_dir/%f'
```

> Pointed to Slaves `/mnt/archive_wal_dir` directory


### Step 3: Restart the DB cluster

```shell
imrul@pc:/$ sudo systemctl restart postgresql-10   # from root user
```

- [If Failed](https://dba.stackexchange.com/questions/196931/how-to-restart-postgresql-server-under-centos-7)

> Sometimes there may have some issues restarting the DB

- **Error:** `psql: error: could not connect to server: No such file or directory Is the server running locally and accepting connections on Unix domain socket "/var/run/postgresql/.s.PGSQL.5432"?`

- Solution: [I experienced this issue when working....](https://stackoverflow.com/questions/31645550/postgresql-why-psql-cant-connect-to-server)

```shell
imrul@pc:/$ sudo systemctl status postgresql-10
imrul@pc:/$ sudo systemctl restart postgresql-10
imrul@pc:/$ pg_lsclusters
imrul@pc:/$ sudo pg_ctlcluster 10 main start
imrul@pc:/$ sudo nano /var/log/postgresql/postgresql-10-main.log
imrul@pc:/$ sudo chmod -R 0700 /var/lib/pgsql/10/data
imrul@pc:/$ sudo systemctl start postgresql
```

## **Major Task**

### Step 1: Create table 1 :: data1

```sql
postgres=# create table pitr.testPITR1 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; --2021-07-27 03:32:31.618857-04
```

### Step 2: Take Base Backup

- Copy the `data` folder to `bkp` folder as `data1`

### Step 3: Create table 2 :: data2

```sql
postgres=# create table pitr.testPITR2 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; 
```

### Step 4: Take Base Backup

- Copy the `data` folder to `bkp` folder as `data2`

### Step 5: Create table 3 

```sql
postgres=# create table pitr.testPITR3 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; 
```

### Step 6: DROP `Table-2` and `Table-3` 

```sql
postgres=# drop table pitr.testpitr2;
postgres=# drop table pitr.testpitr3;
postgres=# select * from current_timestamp;
```


## **Restoring**

### Step 1: Stop the server

```shell
imrul@pc:/$ sudo systemctl stop postgresql-10
```

### Step 2: Get back the `basebackup` folder

- Need to rename `/var/lib/pgsql/10/data` to `/var/lib/pgsql/10/data_bkp"`

- Need to take back copy of `data1` from the remote location

- rename `data1` to `data`


### Step 3: Restore Configuration in `recovery.conf`

- Local Restoring

```conf
restore_command = 'cp  /var/lib/pgsql/10/pgDataPITR/%f %p'
recovery_target_time = '2021-07-27 14:00:00'
```

- Slave Restoring

```conf
restore_command = 'cp   /var/mnt/archive_wal_dir/%f %p'
recovery_target_time = '2021-07-27 14:00:00'
```


### Step 4: Create `recovery.signal` file `[Only for v-13`]

- this `recovery.signal` is just an empty file which indicates that data should start recovering when db starts.

### Step 5: Restart the server

```shell
imrul@pc:/$ sudo systemctl start postgresql-10
```

> If problem may arises related to `Folder Ownership`. Solution below

```shell
imrul@pc:/$ sudo systemctl status postgresql
imrul@pc:/$ sudo systemctl start postgresql
imrul@pc:/$ sudo systemctl restart postgresql
imrul@pc:/$ pg_lsclusters
imrul@pc:/$ sudo pg_ctlcluster 10 main start

imrul@pc:/$ sudo chown -R postgres /var/lib/pgsql/10/data  # change owner of `main` folder to postgres

imrul@pc:/$ sudo nano /var/log/postgresql/postgresql-10-main.log
imrul@pc:/$ sudo chmod -R 0700  /var/lib/pgsql/10/data
imrul@pc:/$ sudo systemctl start postgresql
```

### Step 6: Need to resume the DB from Recovering mode

```sql
postgres=# SELECT pg_wal_replay_resume(); --Move to normal mode
```

---

# **Example**

---

```cmd
pitr 1: 2021-07-26 22:05:27.898092+06
bkp1
pitr 2: 2021-07-26 22:11:39.419188+06
bkp2
pitr 3: 2021-07-26 22:19:20.303399+06
drop 2 & 3: 
```

---

