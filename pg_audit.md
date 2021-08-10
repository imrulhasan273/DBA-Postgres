# **PG Audit**

---

- [Ref1](https://severalnines.com/database-blog/how-to-audit-postgresql-database)

---

### Install pg audit

```shell
yum install pgaudit14_12
```

---

### Add it in the postgresql.conf configuration file

```shell
vi /var/lib/pgsql/13/data/postgresql.conf
```

```conf
shared_preload_libraries = 'pgaudit, pg_stat_statements'
```

---

### Restart DB

```shell
sudo systemctl restart postgresql-13
```

---

### create the extension:

```sql
CREATE EXTENSION pgaudit;
```

```sql
SELECT * FROM pg_available_extensions WHERE name LIKE '%audit%';
```

---

### pgAudit Configuration

```sql
postgres=# SELECT name,setting FROM pg_settings WHERE name LIKE 'pgaudit%';
```

---

### To audit all the reads, writes, and DDL queries, run:

```sql
test1=# set pgaudit.log = 'read,write,ddl';
SET
```

---

### And then, run the following sentences:

```sql
test1=# CREATE TABLE table1 (id int, name text); 
test1=# INSERT INTO table1 (id, name) values (1, 'name1'); 
test1=# INSERT INTO table1 (id, name) values (2, 'name2');
test1=# INSERT INTO table1 (id, name) values (3, 'name3');
test1=# SELECT * FROM table1;
```

---

### If you check the PostgreSQL log file, you will see this:

```sql
2020-11-20 19:17:13.848 UTC [25142] LOG:  AUDIT: SESSION,3,1,DDL,CREATE TABLE,,,"CREATE TABLE table1 (id int, name text);",<not logged>
2020-11-20 19:18:45.334 UTC [25142] LOG:  AUDIT: SESSION,4,1,WRITE,INSERT,,,"INSERT INTO table1 (id, name) values (1, 'name1');",<not logged>
2020-11-20 19:18:52.332 UTC [25142] LOG:  AUDIT: SESSION,5,1,WRITE,INSERT,,,"INSERT INTO table1 (id, name) values (2, 'name2');",<not logged>
2020-11-20 19:18:58.103 UTC [25142] LOG:  AUDIT: SESSION,6,1,WRITE,INSERT,,,"INSERT INTO table1 (id, name) values (3, 'name3');",<not logged>
2020-11-20 19:19:07.261 UTC [25142] LOG:  AUDIT: SESSION,7,1,READ,SELECT,,,SELECT * FROM table1;,<not logged>
```

---

### Enabling pgAudit with ClusterControl

```shell
s9s cluster --setup-audit-logging --cluster-id=ID
```

```shell
s9s job --list
```

```shell
s9s job --log --job-id=163
```

---
