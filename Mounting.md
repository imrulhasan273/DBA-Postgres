# **Mount Network Directories between Servers**

---

## Installation

---

- [Installation Steps](https://sdbaddas.blogspot.com/2016/08/how-to-install-sshfs-on-centosrhelubuntu.html)

- [Video](https://www.youtube.com/watch?v=ThFvgdpFW8s)

### Ubuntu

```shell
~: $ sudo apt install -y sshfs
```

### CentOS (HED Hat)

```shell
~: $ yum install fuse-sshfs 
```

---

## General Syntax

```shell
~: $ sshfs [user@]host:[dir] mountpoint [options]
```

--- 

---

## **Master [IP:10.9.0.222]**

---

### Step 1: Act as `root` user

```shell
imrul@master :~$ sudo -i
```

---

### Step 2: cd to `/var/mnt` directory

```shell
imrul@master :~$ cd /var/mnt
```

---

### Step 3: Create a directory in this location

```shell    
imrul@master :~$ sudo mkdir pgDataPITR          # This folder contains data that to be shared with another server
imrul@master :~$ sudo chmod a+rwx pgDataPITR    # This folder needs permission 
```

---

## **Slave [IP:10.9.0.223]**

---

### Step 1: Act as `root` user

```shell
imrul@slave :~$ sudo -i
```

---

### Step 2: cd to `/mnt` directory

```shell
imrul@slave :~$ cd /mnt
```

---

### Step 3: Create a directory in this location

```shell
imrul@slave :~$ sudo mkdir pgDataPITR           # This folder is mountpoint. All the data of IP:10.9.0.222 are shared on this IP:10.9.0.223
# imrul@slave :~$ sudo chmod a+rwx pgDataPITR   # Not necessary
```

### Step 4: **SSHFS** from `Slave`

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






