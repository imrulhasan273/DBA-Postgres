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

## `pg_ident.conf`

```sql
If present, this file maps an authenticated OS login to a PostgreSQLuser. People sometimes map the OS root account to the PostgresSQLsuperuser account, postgres.
```

## Scripts

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

## The postgresql.conf file 

---

## The pg_hba.conf file 

---
---