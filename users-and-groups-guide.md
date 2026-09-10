# Linux Users & Groups

## Table of Contents

- [Overview](#overview)
- [1. Users and Groups](#section-1)
- [2. UID and GID](#section-2)
- [3. Primary and Supplementary Groups](#section-3)
- [4. /etc/passwd](#section-4)
- [5. /etc/shadow](#section-5)
- [6. /etc/group](#section-6)
- [7. useradd](#section-7)
- [8. usermod](#section-8)
- [9. userdel](#section-9)
- [10. groupadd](#section-10)
- [11. gpasswd](#section-11)
- [12. groupdel](#section-12)
- [13. Checking User and Group Information](#section-13)
- [14. su](#section-14)
- [15. sudo](#section-15)
- [16. su vs sudo](#section-16)
- [17. Root User](#section-17)
- [18. Sudoers Configuration](#section-18)
- [19. Basic Sudoers Syntax](#section-19)
- [20. Allowing Specific Commands](#section-20)
- [21. /etc/sudoers.d/](#section-21)

---

<a id="overview"></a>
## Overview

Linux is a multi-user operating system. Users and groups are used to control:

- Who can log in
- Which files a user can access
- Which processes a user can run
- Which administrative commands a user can execute
- Which resources and services a user can access

The main concepts in this topic are:

- `/etc/passwd`
- `/etc/shadow`
- `/etc/group`
- `useradd`
- `usermod`
- `userdel`
- `groupadd`
- `gpasswd`
- `su`
- `sudo`
- `Sudoers configuration`

---

<a id="section-1"></a>
## 1. Users and Groups

### What is a User?

A Linux user represents an identity.
A user can be:

- A human user
- A system/service account
- The root administrator

Every user has a unique UID (User ID).

Check your current user:

```
whoami
```

Check your user information:

```
id
```

Example:

```
uid=1000/alice gid=1000/alice groups=1000/alice,27/sudo
```

This tells us:

- `uid=1000` → Alice's UID
- `gid=1000` → Alice's primary group ID
- `groups=...` → Groups Alice belongs to

### What is a Group?

A group is a collection of users.
Groups make it easier to manage permissions for multiple users.

For example:

```
developers
├── alice
├── bob
└── charlie
```

Instead of giving permissions to Alice, Bob, and Charlie individually, you can give permissions to the `developers` group.

This works especially well with Linux file permissions.

Example:

```
-rwxrwx--- alice developers project.sh
```

Here:

- `alice` is the owner
- `developers` is the group
- The group members can use the permissions assigned to the group

---

<a id="section-2"></a>
## 2. UID and GID

### UID

UID stands for User ID.
Linux internally identifies users by UID rather than just their username.

Check a user's UID:

```
id alice
```

Example:

```
uid=1001(alice)
```

### GID

GID stands for Group ID.
It identifies a group.

Check group information:

```
getent group developers
```

Example:

```
developers:x:1002:alice,bob
```

Here:

- `developers` → group name
- `1002` → GID

---

<a id="section-3"></a>
## 3. Primary and Supplementary Groups

A user has one primary group and can have multiple supplementary groups.

Example:

```
User: alice

Primary group:
    alice

Supplementary groups:
    developers
    docker
```

Check this with:

```
id alice
```

Example:

```
uid=1001(alice) gid=1001(alice) groups=1001(alice),1002(developers),1003(docker)
```

The group after `gid=` is the primary group.
The groups listed after `groups=` are the user's group memberships.

---

<a id="section-4"></a>
## 4. /etc/passwd

`/etc/passwd` contains information about local user accounts.

View it with:

```
cat /etc/passwd
```

A typical entry looks like:

```
alice:x:1001:1001:Alice:/home/alice:/bin/bash
```

The fields are separated by `:`.
There are seven fields:

```
username:password:UID:GID:GECOS:home:shell
```

For example:

- `alice`
- `x`
- `1001`
- `1001`
- `Alice`
- `/home/alice`
- `/bin/bash`

**Field 1: Username**

`alice`
The user's login name.

**Field 2: Password**

`x`
On modern Linux systems, `x` normally means the password hash is stored in `/etc/shadow`.
The actual password hash should not normally be stored in `/etc/passwd`.

**Field 3: UID**

`1001`
The user's User ID.

**Field 4: GID**

`1001`
The user's primary Group ID.

**Field 5: GECOS**

`Alice`
Usually contains descriptive information such as the user's full name.

**Field 6: Home Directory**

`/home/alice`
The user's home directory.

**Field 7: Login Shell**

`/bin/bash`
The shell used when the user logs in.

---

<a id="section-5"></a>
## 5. /etc/shadow

`/etc/shadow` stores sensitive password and password-aging information.

View it with:

```
sudo cat /etc/shadow
```

A simplified entry may look like:

```
alice:$6$HASH...:20000:0:99999:7:::
```

The fields contain information such as:

- Username
- Password hash
- Last password change
- Minimum password age
- Maximum password age
- Password warning period
- Account-related expiration information

**Why is /etc/shadow protected?**

Password hashes are sensitive.
Normally:

```
ls -l /etc/shadow
```

shows that access is restricted.
Regular users should not be able to read all password hashes.

---

<a id="section-6"></a>
## 6. /etc/group

`/etc/group` contains local group information.

View it with:

```
cat /etc/group
```

Example:

```
developers:x:1002:alice,bob
```

The fields are:

```
group_name:password:GID:members
```

For example:

- `developers`
- `x`
- `1002`
- `alice,bob`

This means:

- Group name → developers
- GID → 1002
- Members → alice, bob

You can also use:

```
getent group developers
```

---

<a id="section-7"></a>
## 7. useradd

`useradd` creates a new user account.

Basic command:

```
sudo useradd alice
```

A more useful example is:

```
sudo useradd -m -s /bin/bash alice
```

**Options:**

| Option | Meaning |
|--------|---------|
| `-m` | Create the user's home directory |
| `-s` | Set the login shell |
| `-d` | Set the home directory |
| `-u` | Specify UID |
| `-g` | Specify primary group |
| `-G` | Specify supplementary groups |

Example:

```
sudo useradd -m -s /bin/bash alice
```

This creates:

- User: alice
- Home: /home/alice
- Shell: /bin/bash

You can verify:

```
id alice
```

and:

```
getent passwd alice
```

---

<a id="section-8"></a>
## 8. usermod

`usermod` modifies an existing user.

**Change Login Shell**

```
sudo usermod -s /bin/bash alice
```

**Change Home Directory**

```
sudo usermod -d /home/newhome alice
```

If you want to move existing home-directory contents as well:

```
sudo usermod -d /home/newhome -m alice
```

**Change Primary Group**

```
sudo usermod -g developers alice
```

**Add User to a Supplementary Group**

```
sudo usermod -aG developers alice
```

**Important: -aG**

This is a very important command to understand.

Use:

```
sudo usermod -aG developers alice
```

The `-a` means append.

Without `-a`:

```
sudo usermod -G developers alice
```

the supplementary group list can be replaced.

For adding a user to a group, normally use:

```
usermod -aG group user
```

---

<a id="section-9"></a>
## 9. userdel

`userdel` removes a user account.

Basic:

```
sudo userdel alice
```

This removes the user account but does not necessarily remove the user's home directory.

To remove the user and their home directory:

```
sudo userdel -r alice
```

Be careful with:

```
sudo userdel -r alice
```

because it removes the user's home directory and associated local mail/spool data where applicable.

Before deleting a real account, check what files and processes belong to that user.

---

<a id="section-10"></a>
## 10. groupadd

`groupadd` creates a new group.

Example:

```
sudo groupadd developers
```

Check it:

```
getent group developers
```

You can also specify a GID:

```
sudo groupadd -g 2000 developers
```

Normally, letting Linux choose an available GID is preferable unless you have a specific reason to control it.

---

<a id="section-11"></a>
## 11. gpasswd

`gpasswd` can manage group membership and group administration.

Add a user to a group:

```
sudo gpasswd -a alice developers
```

Remove a user:

```
sudo gpasswd -d alice developers
```

Check membership:

```
getent group developers
```

Another common way to add users to groups is:

```
sudo usermod -aG developers alice
```

Both approaches are useful to know.

---

<a id="section-12"></a>
## 12. groupdel

`groupdel` removes an existing group from the system.

Basic command:

```
sudo groupdel developers
```

This deletes the group entry from `/etc/group` (and its corresponding entry in `/etc/gshadow` if group passwords are in use).

**Important restrictions**

- You cannot delete a group if it is still set as a user's **primary group**. `groupdel` will refuse to run and report that the group is in use as the primary group of a user.
- Before deleting, check whether any user relies on this group as their primary group:

```
grep developers /etc/passwd
```

or

```
getent passwd | awk -F: '$4 == "1002" {print $1}'
```

(replace `1002` with the group's GID).

- If the group is only a **supplementary** group for some users, `groupdel` will remove it without issue — those users simply lose that supplementary membership. You can confirm membership first with:

```
getent group developers
```

**Typical workflow**

1. Check which users belong to the group:

   ```
   getent group developers
   ```

2. If needed, reassign any user whose primary group is `developers` to a different group:

   ```
   sudo usermod -g users alice
   ```

3. Delete the group:

   ```
   sudo groupdel developers
   ```

4. Verify it's gone:

   ```
   getent group developers
   ```

   This should return nothing if the group was successfully removed.

**Note on files owned by the deleted group**

Deleting a group does not automatically change file ownership. Files that were owned by the deleted group's GID will remain, but the GID will no longer resolve to a group name (it will show as a raw number in listings like `ls -l`). If needed, reassign those files to a valid group with `chgrp`.

---

<a id="section-13"></a>
## 13. Checking User and Group Information

### whoami

Shows the current username:

```
whoami
```

Example:

```
alice
```

### id

Shows UID, GID, and group membership:

```
id alice
```

### groups

Shows group membership:

```
groups alice
```

### getent

Queries account databases using the system's configured name-service mechanisms.

Examples:

```
getent passwd alice
getent group developers
```

This is often preferable to reading `/etc/passwd` or `/etc/group` directly when you want to see the account information Linux resolves.

---

<a id="section-14"></a>
## 14. su

`su` means switch user.

Switch to another user:

```
su alice
```

Switch to root:

```
su -
```

The `-` creates a login shell and loads the target user's login environment.

For example:

```
su - alice
```

Then:

```
whoami
```

returns:

```
alice
```

Exit the session:

```
exit
```

---

<a id="section-15"></a>
## 15. sudo

`sudo` means superuser do.

It allows an authorized user to execute a command with elevated privileges.

Example:

```
sudo apt update
```

Instead of switching to root for an entire session, you can run only the command that needs elevated privileges.

You can also run a command as another user:

```
sudo -u alice whoami
```

The command should output:

```
alice
```

**sudo -i**

Start a root login shell:

```
sudo -i
```

Check:

```
whoami
```

Output:

```
root
```

Exit:

```
exit
```

---

<a id="section-16"></a>
## 16. su vs sudo

The important difference:

| `su` | `sudo` |
|------|--------|
| Switches to another user | Runs a command as another user |
| Often used to become another account | Commonly used for administrative commands |
| Can create a full user session | Usually elevates only the requested command |
| Configuration is different | Controlled by sudoers policy |

Example:

```
su - alice
```

You are now working as Alice.

Whereas:

```
sudo systemctl restart nginx
```

runs one administrative command with elevated privileges.

---

<a id="section-17"></a>
## 17. Root User

`root` is the Linux superuser.

Root has extremely powerful privileges.

Check:

```
id root
```

Typical result:

```
uid=0(root) gid=0(root)
```

UID 0 represents the root account.

Root can:

- Read/write many protected files
- Change ownership and permissions
- Manage users
- Install software
- Start/stop services
- Change system configuration
- Manage processes

Because root has so much power, unnecessary root access should be avoided.

---

<a id="section-18"></a>
## 18. Sudoers Configuration

The sudo policy determines who can use sudo and which commands they can execute.

The main configuration file is:

```
/etc/sudoers
```

There may also be additional configuration files under:

```
/etc/sudoers.d/
```

**Important Rule**

Do not casually edit `/etc/sudoers` with a normal text editor.

Use:

```
sudo visudo
```

`visudo` checks the syntax before saving, helping prevent a broken sudo configuration.

---

<a id="section-19"></a>
## 19. Basic Sudoers Syntax

A simple rule:

```
alice ALL=(ALL) ALL
```

This can be understood roughly as:

```
alice
  ↓
can use sudo on
  ↓
all hosts
  ↓
as
  ↓
all users
  ↓
to run
  ↓
all commands
```

A group can be specified using `%`:

```
%developers ALL=(ALL) ALL
```

The `%` means developers is a group.

---

<a id="section-20"></a>
## 20. Allowing Specific Commands

You can give a user access to specific commands instead of everything.

Example:

```
alice ALL=(root) /usr/bin/systemctl restart nginx
```

This is an example of the principle of least privilege:

Give a user only the privileges required to perform their job.

Be careful with commands that can indirectly provide unrestricted root access.

---

<a id="section-21"></a>
## 21. /etc/sudoers.d/

Instead of putting every custom rule into `/etc/sudoers`, you can create a separate configuration file under:

```
/etc/sudoers.d/
```

For example:

```
sudo visudo -f /etc/sudoers.d/developers
```

Then:

```
%developers ALL=(ALL) ALL
```

This keeps custom configuration separate and easier to manage.