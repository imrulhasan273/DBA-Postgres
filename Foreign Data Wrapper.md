# **Postgres in Linux Server to DBeaver Connection on other PC**

---


[Link](https://stackoverflow.com/questions/16904997/connection-refused-pgerror-postgresql-and-rails)

1. Open `hba.conf` and add below line 

```conf
host    all    all    0.0.0.0/0      md5
```

2. Now open postgresql.conf and add 

```conf
listen_address = '*'
```

---

## **Foreign Data Wrapper MySQL --> PostgreSQL**

---



---

## **Foreign Data Wrapper PostgreSQL --> PostgreSQL**

---

### Create Extension

---

```sql
CREATE EXTENSION postgres_fdw SCHEMA public ;
```

---






