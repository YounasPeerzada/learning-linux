# Linux File Management Commands

A practical reference for managing files and directories in Linux using the command line.

## Table of Contents

1. [Understanding Paths](#1-understanding-paths)
2. [List Files](#2-list-files)
3. [Create Files](#3-create-files)
4. [Create Directories](#4-create-directories)
5. [Change Directory](#5-change-directory)
6. [Print Current Directory](#6-print-current-directory)
7. [Copy Files and Directories](#7-copy-files-and-directories)
8. [Move and Rename](#8-move-and-rename)
9. [Delete Files](#9-delete-files)
10. [Delete Directories](#10-delete-directories)
11. [View File Contents](#11-view-file-contents)
12. [Search for Files](#12-search-for-files)
13. [File Information](#13-file-information)

## 1. Understanding Paths

A path tells Linux where a file or directory is located.

### Absolute Path

An absolute path starts from `/`, the root directory.

```
/home/ubuntu/projects/app.js
```

Example:

```bash
cd /home/ubuntu/projects
```

### Relative Path

A relative path starts from your current directory.

```
projects/app.js
```

Example:

```bash
cd projects
```

### Special Path Symbols

| Symbol | Meaning |
|--------|---------|
| `/` | Root directory |
| `.` | Current directory |
| `..` | Parent directory |
| `~` | Current user's home directory |
| `-` | Previous directory |

Examples:

```bash
cd .
cd ..
cd ~
cd -
```

## 2. List Files

### `ls`

Lists files and directories.

```bash
ls
```

### Long Listing

```bash
ls -l
```

Shows information such as:

- Permissions
- Owner
- Group
- File size
- Modification time
- File name

### Show Hidden Files

```bash
ls -a
```

### Long Listing + Hidden Files

```bash
ls -la
```

### Human-Readable File Sizes

```bash
ls -lh
```

### Common Combination

```bash
ls -lah
```

## 3. Create Files

### `touch`

Creates an empty file.

```bash
touch file.txt
```

Create multiple files:

```bash
touch file1.txt file2.txt file3.txt
```

`touch` can also update the modification timestamp of an existing file.

## 4. Create Directories

### `mkdir`

Creates a directory.

```bash
mkdir projects
```

Create multiple directories:

```bash
mkdir frontend backend
```

### Create Nested Directories

Use `-p`:

```bash
mkdir -p project/src/components
```

This creates:

```
project/
└── src/
    └── components/
```

even if the parent directories don't already exist.

## 5. Change Directory

### `cd`

Changes the current working directory.

```bash
cd projects
```

Go to the parent directory:

```bash
cd ..
```

Go to your home directory:

```bash
cd ~
```

Go to the previous directory:

```bash
cd -
```

Go to the root directory:

```bash
cd /
```

## 6. Print Current Directory

### `pwd`

Displays the path of the current working directory.

```bash
pwd
```

Example output:

```
/home/ubuntu/projects
```

`pwd` means Print Working Directory.

## 7. Copy Files and Directories

### `cp`

Copies files or directories.

#### Copy a File

```bash
cp file.txt backup.txt
```

#### Copy a File to a Directory

```bash
cp file.txt backups/
```

#### Copy Multiple Files

```bash
cp file1.txt file2.txt backups/
```

#### Copy a Directory

Use `-r`:

```bash
cp -r project project-backup
```

`-r` means recursive.

It allows `cp` to copy the directory and everything inside it.

#### Preserve File Attributes

```bash
cp -p file.txt backup.txt
```

## 8. Move and Rename

### `mv`

The `mv` command is used to:

- Move files/directories
- Rename files/directories

#### Move a File

```bash
mv file.txt documents/
```

#### Rename a File

```bash
mv old.txt new.txt
```

#### Move and Rename

```bash
mv old.txt documents/new.txt
```

#### Move a Directory

```bash
mv project /home/ubuntu/
```

## 9. Delete Files

### `rm`

Deletes files.

```bash
rm file.txt
```

Delete multiple files:

```bash
rm file1.txt file2.txt
```

#### Ask Before Deleting

```bash
rm -i file.txt
```

`-i` means interactive.

Linux asks for confirmation before deletion.

#### Force Delete

```bash
rm -f file.txt
```

`-f` means force.

⚠️ Be careful with `rm -f`.

Linux normally does not provide a recycle bin for files deleted using `rm`.

## 10. Delete Directories

### Delete an Empty Directory

Use `rmdir`:

```bash
rmdir empty-folder
```

`rmdir` only works when the directory is empty.

### Delete a Directory and Its Contents

Use:

```bash
rm -r folder
```

`-r` means recursive.

### Force Recursive Deletion

```bash
rm -rf folder
```

⚠️ **Dangerous command**

`rm -rf` can permanently delete large amounts of data without asking for confirmation.

Always check the path carefully before using it.

## 11. View File Contents

### `cat`

Displays the contents of a file.

```bash
cat file.txt
```

Example:

```bash
cat README.md
```

`cat` comes from concatenate.

It can display one file or concatenate multiple files.

### `less`

View a large file one screen at a time.

```bash
less file.txt
```

Useful keys:

| Key | Action |
|-----|--------|
| Space | Next page |
| b | Previous page |
| ↑ / ↓ | Move |
| q | Quit |

### `head`

Shows the beginning of a file.

```bash
head file.txt
```

Show the first 20 lines:

```bash
head -n 20 file.txt
```

### `tail`

Shows the end of a file.

```bash
tail file.txt
```

Show the last 20 lines:

```bash
tail -n 20 file.txt
```

#### Follow a Log File

```bash
tail -f app.log
```

This continuously displays new lines added to the file.

Very useful for watching server logs.

## 12. Search for Files

### `find`

Searches for files and directories.

#### Find a File by Name

```bash
find . -name "file.txt"
```

Search from the current directory.

#### Find All .txt Files

```bash
find . -name "*.txt"
```

#### Find Directories

```bash
find . -type d
```

#### Find Files

```bash
find . -type f
```

#### Find Files by Name

```bash
find /home/ubuntu -name "*.log"
```

## 13. File Information

### `file`

Determines the type of a file.

```bash
file example.txt
```

Example:

```
example.txt: ASCII text
```

It can identify many types of files, including:

- Text files
- Images
- Executables
- PDFs
- Archives

### `stat`

Displays detailed information about a file.

```bash
stat file.txt
```

It can show:

- File size
- Permissions
- Owner
- Access time
- Modification time
- Change time
- Inode