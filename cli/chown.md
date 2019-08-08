# Chown

Abbrev. of `change owner`. Used to change file owner and group information. It can also be used to change file access permission such as read, write, and access.

Syntax: `chown [OPTIONS] {owner-user}:{owner-group} {file/directory}`

**Examples**:

Change file ownership
```
$ ls -l demo.txt
-rw-r--r-- 1 root root 0 Aug 31 05:48 demo.txt

$ chown otandadjaja demo.txt
-rw-r--r-- 1 otandadjaja root 0 Aug 31 05:48 demo.txt
```

Change group ownership
```
$ ls -l demo.txt
-rw-r--r-- 1 otandadjaja root 0 Aug 31 05:48 demo.txt

$ chown :ftp demo.txt
-rw-r--r-- 1 otandadjaja ftp 0 Aug 31 05:48 demo.txt
```

Change ownership recursively
```
$ chown -R otandadjaja dir
```
