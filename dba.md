# **Database Administration**

---

# **Index**

---

1. Configuration Files [Query](#Query-1)
2. Managing Connections [Query](#Query-2)
3. Check for Queries being blocked [Query](#Query-3)
4. Roles [Query](#Query-4)
5. Database Creation [Query](#Query-5)
6. Privileges [Query](#Query-6)
7. Extensions [Query](#Query-7)
8. Backup and Restore [Query](#Query-8)
9. Managing Disk Storage with Tablespaces [Query](#Query-9)
10. Verboten Practices [Query](#Query-10)

---

[Index](#Index)

## **Query-1**

# **Configuration Files**

---

## Introduction

### `postgresql.conf`

> Controls general settings, such as memory allocation, default storage location for new databases, the IP addresses that PostgreSQLlistens on, location of logs, and plenty more.

### `pg_hba.conf`

> Controls access to the server, dictating which users can log in to which databases, which IP addresses can connect, and which authentication scheme to accept.

### `pg_ident.conf`

```sql
If present, this file maps an authenticated OS login to a PostgreSQLuser. People sometimes map the OS root account to the PostgresSQLsuperuser account, postgres.
```

### Scripts

```sql
select
	name,
	setting,
	category
from
	pg_settings
where
	category = 'File Locations';
```


---

## Making Configurations Take Effect 

---

### Reloading

- if context = user

```sql
SELECT pg_reload_conf();
```

```cmd
service postgresql-9.5 reload
```

> You may skip the version in command

---

### Restarting

- if context = postmaster

> You can’t restart with a `PostgreSQLcommand`

```cmd
service postgresql-9.5 restart
```
> You may skip the version in command

---

## The postgresql.conf file 

---

### Key settings

```sql
--Key settings
select
	name,
	context,
	unit,
	setting,
	boot_val,
	reset_val
from
	pg_settings
where
	name in('listen_addresses', 'deadlock_timeout', 'shared_buffers', 'effective_cache_size', 'work_mem', 'maintenance_work_mem')
order by
	context,
	name;
```

```sql
--display settings 
SHOW shared_buffers;
```

```sql
SHOW deadlock_timeout;
```

```sql
--the units for all settings
show all;
```

---

### Querying pg_file_settings

```sql
--Querying pg_file_settings
select
	name,
	sourcefile,
	sourceline,
	setting,
	applied
from
	pg_file_settings
where
	name in ('listen_addresses', 'deadlock_timeout', 'shared_buffers' ,'effective_cache_size', 'work_mem', 'maintenance_work_mem')
order by
	name;
```

---

### change settings

```sql
--Changing the postgresql.conf settings
ALTER SYSTEM SET work_mem = '500MB';
SELECT pg_reload_conf();
```

---

### "server won’t start.”

> “I edited my postgresql.conf and now my server won’t start.” The easiest way to figure out what you screwed up is to look at the log file, located at the root of the data folder, or in the pg_log subfolder. Open the latest file and read what the last line says. The error raised is usually selfexplanatory. A common culprit is setting shared_buffers too high. Another suspect is an old postmaster.pid left over from a failed shutdown. You can safely delete this file, located in the data cluster folder, and try restarting again.


---


## The pg_hba.conf file 

---

### "server is broken"

> “I edited my pg_hba.conf and now my server is broken.” Don’t worry. This happens quite often, but it’s easily recoverable. This error is generally caused by typos or by adding an unavailable authentication scheme. When the postgres service can’t parse pg_hba.conf, it blocks all access just to be safe. Sometime, it won’t even start up. The easiest way to figure out what you did wrong is to read the log file located in the root of the data folder or in
the pg_log subfolder. Open the latest file and read the last line. The error message is usually self-explanatory. If you’re prone to slippery fingers, back up the file prior to editing.

---

---