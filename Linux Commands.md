# **Some Useful Commands in Linux**

---

### Linux Version

```shell
~:$ cat /etc/os-release
```

### Act as `root` user

```shell
~:$ sudo -i
```

### Create a folder

```shell
~:$ mkdir test
```

### Create a file

```shell
~:$ touch test.txt
```

### To Exit from an `user`

```shell
~:$ exit
```

### Enabling `Snaps` and Install gedit on `CentOS 7`

- [Instructions](https://snapcraft.io/install/gedit/centos)

### File/Folder Permissions

```shell
~:$ sudo chmod a+rwx folder # Full Permission on Folder
```

### To Open a File in `gedit` 

```shell
~:$ sudo gedit test.txt
```

### To open a File in `nano`

```shell
~:$ nano test.txt
```

> We can easily `read`, `write`, `edit` etc on that.

### To open a File using in `vi`

```shell
~:$ vi test.txt
```

- `:/search_name` ==> Enter
    - `n` to search next  
    - `i` to insert mode ==> `ESC` to **exit** insert mode

- `:/q!` to quite from file  

- `:/wq!` to write and quite.

### Move a file to a folder 

```shell
~:$ mv test.txt dest   # move file test.txt to a folder dest
```

### Copy a file to another location

```shell
~:$ cp file1.txt file2.txt   # copy file1 as file2
```

### Copy a folder 

```shell
~:$ cp -R folder1 folder2        # Copy folder1 as folder2
```

### Rename a directory

```shell
~:$ mv data data_bkp            # Rename folder data to data_bkp
```

### Count files in a folder

```shell
~:$ ls | wc -l  # 69
```

### Size of a folder

```shell
~:$ sudo du -sh pgDataPITR
```

### To delete everythong in the dir


```shell
~:$ rm *    # inside the current directory::Warning!!!
```

### Delete an empty dir

```shell
~:$ rmdir lampp
```

### Delete a non empty dir

```shell
~:$ rm -rf lampp       # Warning!!!
```

---
