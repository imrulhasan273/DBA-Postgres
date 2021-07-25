# **Point In Time Recovery (PITR)**

---

---

## Flow 

1. TK Backup
2. Loading backup [`dump` file]
3. Taking Base Backup
    - `data` folder copy to remote location
    - Run `traditional` commands of `basebackup`
4. Some Operation [DDL/DML] 
    - Note the `time` after operations
5. Drop the **point(4)** `tables` 
6. Restore the point(4) table using PITR
    - Remove the `data` folder
    - Take back the `Base Backup` from remote location to where the data folder exists
    - PITR using `recovery_target_time` and `recovery_command`

---