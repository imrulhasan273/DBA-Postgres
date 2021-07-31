# **Error In Postgres DB**

---

## Problem 1: PostgreSQL Error: cannot execute in a read-only transaction

---

```sql
ERROR:  cannot execute CREATE DATABASE in a read-only transaction
Error: cannot execute DROP DATABASE in a read-only transaction
ERROR:  cannot execute CREATE TABLE in a read-only transaction
Error: cannot execute DROP TABLE in a read-only transaction
cannot execute INSERT in a read-only transaction
```

### Reason

> `Reason is simple, PostgreSQL server is recovery(read-only) or standby mode.`

### Resolution

1. We have to remove recovery(read-only) or standby mode.

2. Go to your data or main directory then remove `standby.signal` and `recovery.signal` files.

- Before performing following action, take backup of these files in some other location.

- File location in(Ubuntu change your respective version nuymber)

3. After removing signal files, restart PostgreSQL cluster.

4. Then, you will able to connect to the server and perform all actions as usual.

---


alter database postgres set default_transaction_read_only = off;

---

## Problem 2: is the server running locally and accepting connections on unix domain socket "var/run/postgresql/".s.pgsql.5432

---

- Solution: [I experienced this issue when working with PostgreSQL on Ubuntu 18.04.](https://stackoverflow.com/questions/31645550/postgresql-why-psql-cant-connect-to-server)

---



---

## Base Backup

---

wal_level = hot_standby
max_wal_senders = 5

```shell
# from postgres user
postgres@data $ pg_basebackup -h localhost -D /var/lib/postgresql/13/base_bkp/data$(date +_%y%m%d_%H%M)       # FOLDERS
postgres@data $ pg_basebackup -h localhost -D /var/lib/postgresql/13/base_bkp/data$(date +_%y%m%d_%H%M).tar   # A tar file
```

```shell
tar -D /var/lib/postgresql/13/base_bkp/base_bkp__210731_1751.tar -C /var/lib/postgresql/13/main/pg_wal
```

