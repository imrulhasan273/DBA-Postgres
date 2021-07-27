# **Shell Commands**

---




- backup.sh

```bash
#!/bin/bash
cd /home/imrul/imrul
cp -R data base_bkp/data$(date +_%y%m%d_%H%M)

```

- viewbackup.sh

```bash
#!/bin/bash
cd /home/imrul/imrul/base_bkp
ls

```
- restore.sh

```bash
#!/bin/bash
cd /home/imrul/imrul
mv data bkp/data_bkp$(date +_%y%m%d_%H%M)
cp -R base_bkp/data_210727_2344 data

```

- restore.sh

```bash
#!/bin/bash
cd /home/imrul/imrul
mv data bkp/data_bkp$(date +_%y%m%d_%H%M)
read bkpname
cp -R base_bkp/$bkpname data

```

chmod +x backup.sh

#!/bin/sh
# This is a comment!
echo Hello World 