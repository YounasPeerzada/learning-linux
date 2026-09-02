# Linux File Permissions and Ownership

## Table of Contents

* [1. Introduction](#1-introduction)
* [2. Viewing File Permissions](#2-viewing-file-permissions)
* [3. Basic Permissions](#3-basic-permissions)
* [4. Permission Categories](#4-permission-categories)
* [5. Numeric Permissions](#5-numeric-permissions)
* [6. Changing Permissions with chmod](#6-changing-permissions-with-chmod)
* [7. File Ownership](#7-file-ownership)
* [8. The chown Command](#8-the-chown-command)
* [9. Change the Owner](#9-change-the-owner)
* [10. Change the Owner and Group](#10-change-the-owner-and-group)
* [11. Change Only the Group](#11-change-only-the-group)
* [13. Checking Ownership](#13-checking-ownership)
* [14. Permissions and Ownership Together](#14-permissions-and-ownership-together)
* [15. Directory Permissions](#15-directory-permissions)
* [16. Practical Examples](#16-practical-examples)
* [17. Important Commands Cheat Sheet](#17-important-commands-cheat-sheet)
* [18. Key Concepts to Remember](#18-key-concepts-to-remember)

---

## 1. Introduction

Linux uses a permission system to control who can read, write, and execute files and directories.

Every file and directory has:

* An owner
* A group
* Permissions for the owner
* Permissions for the group
* Permissions for others

Understanding permissions and ownership is essential for Linux administration, security, and troubleshooting.

---

## 2. Viewing File Permissions

Use the `ls -l` command:

```bash
ls -l
```

Example:

```text
-rwxr-xr-- 1 alice developers 1200 Sep 2 20:00 script.sh
```

The first part `-rwxr-xr--` represents the file type and permissions.

### Permission Structure

```text
- rwx r-x r--
│   │   │
│   │   └── Others
│   └────── Group
└────────── Owner
```

The first character represents the file type:

| Symbol | Meaning       |
| ------ | ------------- |
| `-`    | Regular file  |
| `d`    | Directory     |
| `l`    | Symbolic link |

---

## 3. Basic Permissions

Linux has three basic permissions:

| Permission | Symbol | Meaning                 |
| ---------- | ------ | ----------------------- |
| Read       | `r`    | View file contents      |
| Write      | `w`    | Modify file contents    |
| Execute    | `x`    | Run a file as a program |

For example, `rwx` means:

* `r` = read
* `w` = write
* `x` = execute

---

## 4. Permission Categories

Permissions are assigned to three categories:

### Owner

The user who owns the file.

### Group

Users who belong to the file's group.

### Others

Everyone else.

Example: `-rwxr-xr--`

Can be interpreted as:

```text
Owner:  rwx
Group:  r-x
Others: r--
```

Therefore:

* Owner can read, write, and execute.
* Group can read and execute.
* Others can only read.

---

## 5. Numeric Permissions

Linux permissions can also be represented using numbers.

| Permission    | Value |
| ------------- | ----- |
| Read (`r`)    | 4     |
| Write (`w`)   | 2     |
| Execute (`x`) | 1     |
| No permission | 0     |

The values are added together.

### Examples

```text
r--  = 4
-w-  = 2
--x  = 1
rw-  = 6
r-x  = 5
-wx  = 3
rwx  = 7
```

### Common Permission Sets

**755**

Means:

```text
Owner:  7 = rwx
Group:  5 = r-x
Others: 5 = r-x
```

**644**

Means:

```text
Owner:  6 = rw-
Group:  4 = r--
Others: 4 = r--
```

---

## 6. Changing Permissions with chmod

The `chmod` command changes file permissions.

### Numeric Method

```bash
chmod 755 script.sh
```

This gives:

* Owner: read, write, execute
* Group: read, execute
* Others: read, execute

Another example:

```bash
chmod 644 file.txt
```

This gives:

* Owner: read, write
* Group: read
* Others: read

### Symbolic Method

You can modify individual permissions using symbols:

```bash
chmod u+x script.sh
```

Here:

* `u` = user/owner
* `g` = group
* `o` = others
* `a` = all

Examples:

```bash
chmod u+x script.sh
chmod g+w file.txt
chmod o-r file.txt
chmod a+x script.sh
```

### Assign Permissions Directly

You can also explicitly set the permissions for a category using `=`.

For example:

```bash
chmod u=rwx file.txt
```

This sets the owner's permissions to `rwx`.

You can set permissions for multiple categories at once:

```bash
chmod u=rwx,g=rx,o=r file.txt
```

This means:

```text
Owner:  rwx
Group:  r-x
Others: r--
```

You can also remove all permissions from a category:

```bash
chmod o= file.txt
```

Or set different permissions for each category:

```bash
chmod u=rw,g=r,o= file.txt
```

Result:

```text
Owner:  rw-
Group:  r--
Others: ---
```

---

## 7. File Ownership

Linux files have an owner and a group.

Use:

```bash
ls -l
```

Example:

```text
-rw-r--r-- 1 alice developers 1200 Sep 2 20:00 file.txt
```

Here:

* Owner = `alice`
* Group = `developers`

The ownership information is separate from the permission information.

---

## 8. The chown Command

`chown` means **change owner**.

It is used to change the owner and/or group of a file or directory.

### Basic syntax

```bash
chown [options] OWNER[:GROUP] FILE
```

---

## 9. Change the Owner

Example:

```bash
sudo chown bob file.txt
```

This changes the owner of `file.txt` to `bob`.

Before:

```text
alice developers file.txt
```

After:

```text
bob developers file.txt
```

The group remains unchanged.

---

## 10. Change the Owner and Group

Use:

```bash
sudo chown bob:developers file.txt
```

This changes:

* Owner = `bob`
* Group = `developers`

---

## 11. Change Only the Group

You can use:

```bash
sudo chown :developers file.txt
```

This changes the group to `developers` while keeping the existing owner.

You can also use `chgrp` for changing only the group:

```bash
sudo chgrp developers file.txt
```

---

## 13. Checking Ownership

Use:

```bash
ls -l file.txt
```

For more detailed information:

```bash
stat file.txt
```

Example:

```text
File: file.txt
Access: (0644/-rw-r--r--)
Uid: ( 1001/ alice)
Gid: ( 1002/developers)
```

---

## 14. Permissions and Ownership Together

Permissions and ownership work together.

Consider:

```text
-rwxr-x--- 1 alice developers script.sh
```

This means:

* Owner: `alice`
* Group: `developers`
* Others: no permissions

Permissions:

```text
alice       = rwx
developers  = r-x
others      = ---
```

If Bob is a member of the `developers` group, Bob can read and execute the script but cannot modify it.

---

## 15. Directory Permissions

Permissions behave slightly differently for directories.

| Permission | Directory meaning                 |
| ---------- | --------------------------------- |
| `r`        | List directory contents           |
| `w`        | Create, delete, or rename entries |
| `x`        | Enter/traverse the directory      |

For example:

```bash
chmod 755 mydir
```

Usually means:

```text
Owner:  rwx
Group:  r-x
Others: r-x
```

The `x` permission is especially important for directories because it allows users to access/traverse them.

---

## 16. Practical Examples

### Example 1: Make a Script Executable

Check permissions:

```bash
ls -l script.sh
```

Add execute permission:

```bash
chmod +x script.sh
```

Run it:

```bash
./script.sh
```

### Example 2: Protect a Private File

Set permissions so only the owner can read and write:

```bash
chmod 600 secret.txt
```

Result:

```text
-rw-------
```

### Example 3: Change Project Ownership

```bash
sudo chown -R alice:developers project/
```

Verify:

```bash
ls -ld project/
ls -l project/
```

### Example 4: Standard File Permission

A common permission for a normal readable file is:

```bash
chmod 644 file.txt
```

Result:

```text
-rw-r--r--
```

The owner can read and write, while the group and others can read.

### Example 5: Standard Directory Permission

A common directory permission is:

```bash
chmod 755 mydir
```

Result:

```text
drwxr-xr-x
```

The owner can read, write, and enter the directory. Group and others can read and enter it but cannot create or delete files there.

---

## 17. Important Commands Cheat Sheet

| Command | Purpose                                   |
| ------- | ----------------------------------------- |
| `ls -l` | View permissions and ownership            |
| `chmod` | Change permissions                        |
| `chown` | Change owner and/or group                 |
| `chgrp` | Change group                              |
| `stat`  | Display detailed file information         |
| `id`    | Display current user's UID/GID and groups |

### Examples

```bash
ls -l file.txt
chmod 644 file.txt
chmod u+x script.sh
sudo chown bob file.txt
sudo chown bob:developers file.txt
sudo chgrp developers file.txt
stat file.txt
id
```

---

## 18. Key Concepts to Remember

* Permissions control access.
* Ownership identifies the user and group associated with a file.
* `r` means read.
* `w` means write.
* `x` means execute for files and traverse/enter for directories.
* `chmod` changes permissions.
* `chown` changes ownership.
* `chgrp` changes the group.
* `chown` can change the owner and/or group of a file or directory.
* Always be careful when using `sudo` and recursive ownership changes.
