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