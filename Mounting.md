# **Mount Network Directories between Servers**

---

## Installation

---



---

## General Syntax

```shell
~: $ sshfs [user@]host:[dir] mountpoint [options]
```

--- 

---

## **Master [IP:10.9.0.222]**

---

### Act as `root` user

```shell
imrul@master :~$ sudo -i
```

---

### cd to `/var/mnt` directory

```shell
imrul@master :~$ cd /var/mnt
```

---

### Create a directory in this location

```shell    
imrul@master :~$ sudo mkdir pgDataPITR          # This folder contains data that to be shared with another server
imrul@master :~$ sudo chmod a+rwx pgDataPITR    # This folder needs permission 
```

---

## **Slave [IP:10.9.0.223]**

---

### Act as `root` user

```shell
imrul@slave :~$ sudo -i
```

---

### cd to `/mnt` directory

```shell
imrul@slave :~$ cd /mnt
```

---

### Create a directory in this location

```shell
imrul@slave :~$ sudo mkdir pgDataPITR           # This folder is mountpoint. All the data of IP:10.9.0.222 are shared on this IP:10.9.0.223
# imrul@slave :~$ sudo chmod a+rwx pgDataPITR   # Not necessary
```

### SSHSF from `Slave`

```shell
imrul@slave :~$ sshfs imrul@10.9.0.222:/var/mnt/pgDataPITR /mnt/pgDataPITR
```

> Provide the password `10.9.0.222`

- username  : user
- Source IP : 10.9.0.222
- Source DIrectory (Master) : `/var/mnt/pgDataPITR`
- Destination IP : 10.9.0.223
- Destination Directory :  `/mnt/pgDataPITR`

---






