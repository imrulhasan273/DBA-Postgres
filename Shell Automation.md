# **Shell Commands**

---


> chmod +x backup.sh


- base.sh

```bash
#!/bin/bash
cd /home/imrul/imrul
cp -R data base_bkp/data$(date +_%y%m%d_%H%M)

```

- view.sh

```bash
#!/bin/bash
cd /home/imrul/imrul/base_bkp
ls

```

- restore.sh

```bash
#!/bin/bash
cd /home/imrul/imrul
read bkpname
DIR="base_bkp/"$bkpname
echo "DIR: ${DIR}"
if [ -d "$DIR" ]; then
    # Take action if $DIR exists. #
    echo "${DIR} is exists"
    mv data bkp/data_bkp$(date +_%y%m%d_%H%M)
    cp -R base_bkp/$bkpname data
fi

```

---

# Linux 13

---


## Instruction

- `base_bkp` contains base backup

- `bkp` contains data files that need to be backed up before restoring

---

- base.sh

```bash
#!/bin/bash
cd /var/lib/pgsql/13/
cp -R data base_bkp/data$(date +_%y%m%d_%H%M)

```

- view.sh

```bash
#!/bin/bash
cd /var/lib/pgsql/13/base_bkp
ls

```

- restore.sh

```bash
#!/bin/bash
cd /var/lib/pgsql/13/
read bkpname
DIR="base_bkp/"$bkpname
if [ -d "$DIR" ]; then
    # Take action if $DIR exists. #
    echo "${DIR} is exists"
    mv data bkp/data_bkp$(date +_%y%m%d_%H%M)
    cp -R base_bkp/$bkpname data
else
  ###  Control will jump here if $DIR does NOT exists ###
  echo "${DIR} is not exists"
#   exit 1
fi

```


## Taking Basebackup

---

- To take base backup

```shell
~$ bash /var/lib/pgsql/13/scripts/base.sh
```

---

## Restore Basebackup

---

- To view base backups

```shell
~$ bash /var/lib/pgsql/13/scripts/view.sh
```

- restore the baseback you want

```shell
~$ bash /var/lib/pgsql/13/scripts/restore.sh
```

---





