# **PITR Final on (CentOS) Master-Slave(Time Lag)**

---

# **Introduction**

---

## **Overall Planning**

![](i/20.png)

### Details

1. Master Server on left and Slave Server on right
2. Slave Server is lagged (by any duration configured in slave) from Master Server
3. Wal files of Master Server is archiving in archive_wal_dir dynamically
4. archive_wal_dir in Master Server is mounted on archive_wal_dir in Slave Server
5. PITR will be perform on Slave Server and archive_wal_dir  in slave server


## **Scenario**

![](i/21.png)

### Details

1. Master Server has table PITR1, PITR2, PITR3 with 18 lac data each at day 1
2. Slave Server Synced  PITR1 (18 lac), PITR2 (18 lac) , PITR3 (18 lac) after 3 days later. (day 4)
3. After 3 days (day 4) new Data Added in PITR3 (18 lac) 
4. DROP PITR2 from Master at day 4
5. Now Master server have data of PITR1 (18 lac) and PITR3 (36 lac)
6. Slave Server will sync with Master at Day 7 (3 days later).
7. Slaves have data PITR1 (18 lac), PITR2 (18 lac) , PITR3 (18 lac) between day 4 - day 6
8. So if we want to recover the lost data we need to perform PITR between day 4 and day 6 otherwise master data will be sync with slave on day 7
9. If we want to perform PITR upto point(4). 
	- Note the time. 
	- Stop the slave server, 
	- Turn off standby mode, 
	- Turn on recovery mode 
11. Performing PITR upto point (4) 
12. Data Restored

---

---

# **Chapter-01: Postgres-13 Installation**

---


---

# **Chapter-02: Archive Folder Mounting**

---


---

# **Chapter-03: Archiving in Master**

---


---

# **Chapter-04: Streaming Replication**

---


---

# **Chapter-05: Delay Replication**

---


---

# **Chapter-06:  Incident made in Master DB**

---


---

# **Chapter-07: PITR on Slave Server**

---


---

# **Chapter-08: Cautions**

---





