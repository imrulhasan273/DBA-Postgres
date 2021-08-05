# **Migration**

---

## Useful Links

---

- [Link1](https://www.cybertec-postgresql.com/en/upgrading-and-updating-postgresql/)
- [Link2](https://stackoverflow.com/questions/60409585/how-to-upgrade-postgresql-database-from-10-to-12-without-losing-data-for-openpro)
- [Link3](https://www.enterprisedb.com/docs/epas/latest/epas_upgrade_guide/07_migration/)
- [Link4](https://docs.bmc.com/docs/btco113/migrating-the-data-from-postgresql-9-x-to-10-x-800581922.html)
- [Link5](https://www.postgresql.org/docs/13/pgupgrade.html)
- [Link6](https://www.postgresql.org/docs/12/upgrading.html)
- [Link7](https://www.percona.com/blog/2020/07/28/migrating-to-postgresql-version-13-incompatibilities-you-should-be-aware-of/)

---

# **Link1**

---

## Two Kinds of Migrations

- **Option 1:** Updating PostgreSQL
- **Option 2:** Upgrading PostgreSQL

---

### Option 1: Updating PostgreSQL [Not Our Case]

---

- **PostgreSQL 13.0 to PostgreSQL 13.1	[Update is enough]**


- **Way:**

```shell
pg_dump             # Dump / reload not required
```

```shell
Tooling	            # pg_dump			
Task	            # Minor release update				
Reloading data      # Not needed
Downtime needed     # Close to zero					
```

- Step 1: **install** the `new binaries` and **restart** the database. `[no need to reload the data.]`

---

### Option 2: Upgrading PostgreSQL	  [Our Case]

---

- **PostgreSQL 9.5, 9.6, 10, 11 or 12 to PostgreSQL 13.1	[upgrade is needed.]**

- **Ways:**

```shell
pg_dump             # Dump / reload	 :: might take a lot of time     				  :: time increased with db size
pg_upgrade          # binary level	  	 :: cp data from old dir to new dir				  :: time increased with db size
pg_upgrade –link    # In-place upgrades :: new_data and old_data dir ==> same filesystem :: ->0 downtime, amount of data != limiting factor 
```

- **Waly 1:**

```shell
Tooling	            # pg_upgrade			
Task	            # Major release update			
Reloading data		# Copy is needed
Downtime needed     # Downtime is needed			
```		

- **Way 2:**

```shell
Tooling	            # pg_upgrade –link			
Task	            # Major release update			
Reloading data		# Only hardlinks
Downtime needed     # Close to zero			
```		

> Note: 
1. pg_upgrade is never destructive.	
    - if things go wrong, you can always delete the new data directory and start from scratch.
		
		












