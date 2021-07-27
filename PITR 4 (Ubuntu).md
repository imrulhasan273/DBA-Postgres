

- [Blog on PGSQL](https://pgdash.io/blog/postgres-13-getting-started.html)

---

# **Some Useful Commands in Linux**

---

### To Exit from DB user

```shell
~:$ exit
```

### Permission Related

```shell
~:$ sudo chmod a+rwx folder	# Full Permission on Folder
```

### To Open a File using cmd

```shell
~:$ sudo gedit test.txt
```

---

# **Postgres Installation**

---

## Uninstalling 

```shell
~:$ sudo apt-get --purge remove postgresql
~:$ sudo apt-get purge postgresql*
~:$ sudo apt-get --purge remove postgresql postgresql-doc postgresql-common

~:$ sudo rm -rf /var/lib/postgresql/
~:$ sudo rm -rf /var/log/postgresql/
~:$ sudo rm -rf /etc/postgresql/
```

---

## Install Postgres 13


### Step 1: add the repository

```cmd
~:$ sudo tee /etc/apt/sources.list.d/pgdg.list <<END
deb http://apt.postgresql.org/pub/repos/apt/ focal-pgdg main
END
```

### Step 2: get the signing key and import it

```shell
~:$ wget https://www.postgresql.org/media/keys/ACCC4CF8.asc
sudo apt-key add ACCC4CF8.asc
```

### Step 3: fetch the metadata from the new repo

```shell
~:$ sudo apt-get update
```

### Step 4:

```shell
~:$ sudo apt-get install -y postgresql-13
```

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
ALTER USER postgres WITH PASSWORD 'IMRUL';  --need to be run from psql
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
```

---

## **Backup and Restore**

---

### Backup db using pg_dump

```shell
~:$ pg_dump -h localhost -p 5432 -U postgres -d postgres -n pitr >> /var/lib/postgresql/13/pg_dump/pitr.sql
```

### Restore db using psql

```shell
~:$ psql -h localhost -p 5432 -U postgres -d postgres -f /var/lib/postgresql/13/pg_dump/pitr.sql
```

---

---

# **POINT IN TIME RECOVERY** - LINUX**

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

~:$ less /etc/postgresql/13/main/postgresql.conf    # to open the file using Command 

~:$ more /etc/postgresql/13/main/postgresql.conf    # to open the file using Command 
```


## **Archiving**


### Step 1: Open 'postgresql.conf' using cmd

```shell
imrul@pc:/$ sudo gedit /etc/postgresql/13/main/postgresql.conf  # Open the file using text editor to edit
```


### Step 2: Need to configure the archiving 'postgresql.conf'

```text
archive_mode = on
archive_command = 'test ! -f /var/lib/postgresql/13/pgDataPITR/%f && cp %p /var/lib/postgresql/13/pgDataPITR/%f'
```

### Step 3: Restart the DB cluster

```shell
imrul@pc:/$ sudo systemctl restart postgresql   # from root user
```

> Sometimes there may have some issues restarting the DB

- **Error:** `psql: error: could not connect to server: No such file or directory Is the server running locally and accepting connections on Unix domain socket "/var/run/postgresql/.s.PGSQL.5432"?`

- Solution: [I experienced this issue when working....](https://stackoverflow.com/questions/31645550/postgresql-why-psql-cant-connect-to-server)

```shell
imrul@pc:/$ sudo systemctl status postgresql
imrul@pc:/$ sudo systemctl restart postgresql
imrul@pc:/$ pg_lsclusters
imrul@pc:/$ sudo pg_ctlcluster 13 main start
imrul@pc:/$ sudo nano /var/log/postgresql/postgresql-13-main.log
imrul@pc:/$ sudo chmod -R 0700 /var/lib/postgresql/13/main
imrul@pc:/$ sudo systemctl start postgresql
```

## **Major Task**

### Step 1: Create table 1 :: main1

```sql
postgres=# create table pitr.testPITR1 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; 
```

### Step 2: Take Base Backup

- Copy the `main` folder to `bkp` folder as `main1`

### Step 3: Create table 2 :: main2

```sql
postgres=# create table pitr.testPITR2 as select * from pg_class, pg_description;  ---DDL activity
postgres=# select * from current_timestamp; 
```

### Step 4: Take Base Backup

- Copy the main folder to bkp folder as main2

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
imrul@pc:/$ sudo systemctl stop postgresql
```

### Step 2: Restore Configuration in `postgres.conf`

```text
restore_command = 'cp /var/lib/postgresql/13/pgDataPITR/%f %p'
recovery_target_time = '2021-07-26 22:19:20.303399+06'
```

### Step 3: Create `recovery.signal` file

- this `recovery.signal` is just an empty file which indicates that data should start recovering when db starts.

### Step 4: Restart the server

```shell
imrul@pc:/$ sudo systemctl restart postgresql
```

> If problem may arises related to `folder ownership`. Solution below

```shell
imrul@pc:/$ sudo systemctl status postgresql
imrul@pc:/$ sudo systemctl start postgresql
imrul@pc:/$ sudo systemctl restart postgresql
imrul@pc:/$ pg_lsclusters
imrul@pc:/$ sudo pg_ctlcluster 13 main start

imrul@pc:/$ sudo chown -R postgres /var/lib/postgresql/13/main  # change owner of `main` folder to postgres

imrul@pc:/$ sudo nano /var/log/postgresql/postgresql-13-main.log
imrul@pc:/$ sudo chmod -R 0700 /var/lib/postgresql/13/main
imrul@pc:/$ sudo systemctl start postgresql
```

### Step 4: Need to resume the DB from Recovering mode

```sql
postgres=# SELECT pg_wal_replay_resume(); --Move to normal mode
```

---

---

# **Example**

---

pitr 1: 2021-07-26 22:05:27.898092+06
bkp1
pitr 2: 2021-07-26 22:11:39.419188+06
bkp2
pitr 3: 2021-07-26 22:19:20.303399+06
drop 2 & 3: 











