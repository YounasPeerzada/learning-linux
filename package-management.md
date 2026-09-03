# Ubuntu Package Management

A complete reference for package management on **Ubuntu Linux**.

---

## Table of Contents

1. [What is Package Management?](#1-what-is-package-management)
2. [What is a Package?](#2-what-is-a-package)
3. [APT](#3-apt)
4. [dpkg](#4-dpkg)
5. [APT vs dpkg](#5-apt-vs-dpkg)
6. [Repositories](#6-repositories)
7. [Updating Package Information](#7-updating-package-information)
8. [Upgrading Packages](#8-upgrading-packages)
9. [Installing Packages](#9-installing-packages)
10. [Removing Packages](#10-removing-packages)
11. [Purging Packages](#11-purging-packages)
12. [Removing Unused Dependencies](#12-removing-unused-dependencies)
13. [Searching for Packages](#13-searching-for-packages)
14. [Package Information](#14-package-information)
15. [Checking Installed Packages](#15-checking-installed-packages)
16. [Checking Package Versions](#16-checking-package-versions)
17. [Package Dependencies](#17-package-dependencies)
18. [Conclusion](#18-conclusion)

---

# 1. What is Package Management?

**Package management** is the system Ubuntu uses to install, remove, update, upgrade, and manage software.

Instead of manually downloading software and its dependencies, Ubuntu can manage them through a package manager.

For example:

```bash
sudo apt install git
```

APT downloads Git and any required dependencies and installs them.

---

# 2. What is a Package?

A **package** is a collection of files and metadata required to install a piece of software.

Ubuntu primarily uses Debian packages with the:

```text
.deb
```

extension.

A package can contain:

* Program files
* Configuration files
* Documentation
* Libraries
* Metadata
* Dependency information
* Installation/removal information

---

# 3. APT

APT stands for:

**Advanced Package Tool**

APT is the main high-level package-management tool used on Ubuntu.

Example:

```bash
sudo apt install nginx
```

Basic structure:

```text
sudo
 ↓
Administrator privileges

apt
 ↓
Package management tool

install
 ↓
Operation

nginx
 ↓
Package name
```

APT is generally the preferred tool for normal Ubuntu package management because it can handle package repositories, downloads, dependencies, and installation.

---

# 4. dpkg

`dpkg` is the lower-level package management system used by Ubuntu and Debian.

It works directly with `.deb` packages.

Example:

```bash
sudo dpkg -i package.deb
```

Useful commands:

```bash
dpkg -l
```

List installed packages.

```bash
dpkg -L package
```

List files installed by a package.

```bash
dpkg -s package
```

Show package status.

```bash
dpkg -S /path/to/file
```

Find which package owns a file.

---

# 5. APT vs dpkg

The relationship can be simplified as:

```text
APT
 ↓
Dependency management
 ↓
Package downloading
 ↓
dpkg
 ↓
.deb installation
```

### APT

APT is a **high-level package management tool**.

It can:

* Download packages
* Manage repositories
* Resolve dependencies
* Install packages
* Remove packages
* Upgrade packages

### dpkg

`dpkg` is a **lower-level package management system**.

It primarily works with local `.deb` packages and maintains information about installed Debian packages.

For normal Ubuntu package management, APT is generally the easier choice.

---

# 6. Repositories

A **repository** is a server containing Ubuntu packages.

Instead of manually downloading software from random websites, APT can obtain software from configured repositories.

```text
Ubuntu Computer
      │
      │ APT
      ▼
Ubuntu Repository
      │
      ▼
.deb Package
      │
      ▼
Installed Software
```

Repositories provide package files along with package information and metadata that APT uses to find and manage software.

---

# 7. Updating Package Information

Use:

```bash
sudo apt update
```

This downloads the latest package information from configured repositories.

It does **not** normally upgrade your installed packages.

Think:

```text
apt update
=
"Find out what updates are available."
```

For example:

```bash
sudo apt update
```

After running this command, APT knows about the latest available package versions from the configured repositories.

---

# 8. Upgrading Packages

Use:

```bash
sudo apt upgrade
```

This installs available upgrades for installed packages.

A common workflow is:

```bash
sudo apt update
sudo apt upgrade
```

Remember:

```text
update  → refresh package information

upgrade → install available updates
```

It is common to run `apt update` before `apt upgrade` so that APT has current information about available versions.

---

# 9. Installing Packages

Install a package:

```bash
sudo apt install package
```

Example:

```bash
sudo apt install git
```

Install multiple packages:

```bash
sudo apt install git curl wget
```

APT can automatically install the dependencies required by the packages you request.

For example:

```bash
sudo apt install nginx
```

APT may install additional packages required for Nginx to work.

---

# 10. Removing Packages

Remove a package:

```bash
sudo apt remove package
```

Example:

```bash
sudo apt remove nginx
```

`remove` normally removes the package but may leave configuration files behind.

Difference:

```text
apt remove
     ↓
Remove the installed package
     ↓
Some configuration files may remain
```

This can be useful when you want to remove a program while keeping its configuration for possible future use.

---

# 11. Purging Packages

To remove the package and its package-managed configuration files:

```bash
sudo apt purge package
```

Example:

```bash
sudo apt purge nginx
```

Difference:

```text
remove
  ↓
Remove package

purge
  ↓
Remove package + package-managed configuration
```

Use `purge` when you want to remove a package and its configuration files managed by the package system.

Note that configuration or other files created manually by the user may not necessarily be removed by `apt purge`.

---

# 12. Removing Unused Dependencies

Ubuntu may install dependencies automatically when you install another package.

If those dependencies are no longer needed:

```bash
sudo apt autoremove
```

This removes packages that were automatically installed and are no longer required.

For example:

```text
Application
    │
    ├── Dependency A
    ├── Dependency B
    └── Dependency C
```

If the application is removed and those dependencies are no longer required by anything else, `autoremove` can clean them up.

Use:

```bash
sudo apt autoremove
```

---

# 13. Searching for Packages

Search for a package:

```bash
apt search package
```

Example:

```bash
apt search nginx
```

You can also search using keywords:

```bash
apt search web server
```

APT searches the available package information and displays packages matching the search terms.

This is useful when you know what type of software you need but do not know the exact package name.

---

# 14. Package Information

Use:

```bash
apt show package
```

Example:

```bash
apt show nginx
```

This can show information such as:

* Package name
* Version
* Architecture
* Description
* Dependencies
* Package size
* Repository information
* Maintainer information

For example:

```bash
apt show git
```

This allows you to inspect package information before installing a package.

---

# 15. Checking Installed Packages

List installed packages:

```bash
apt list --installed
```

You can also use:

```bash
dpkg -l
```

Search installed packages:

```bash
apt list --installed | grep nginx
```

The `dpkg -l` command provides a list of packages known to `dpkg`, including their installation status.

---

# 16. Checking Package Versions

Use:

```bash
apt policy package
```

Example:

```bash
apt policy nginx
```

You may see:

```text
Installed: 1.24.x
Candidate: 1.24.x
```

Meaning:

```text
Installed
    ↓
Currently installed version

Candidate
    ↓
Version APT would currently install
```

`apt policy` is useful when you want to compare the installed version with the version available from your configured repositories.

---

# 17. Package Dependencies

A **dependency** is another package required by a package.

Example:

```text
Application A
    │
    ├── Library B
    ├── Library C
    └── Library D
```

When you run:

```bash
sudo apt install application-a
```

APT can automatically install the required dependencies.

This is one of the major advantages of package management.

Instead of manually finding and installing every required library, APT can determine the dependencies and install the necessary packages.

A simplified dependency process looks like this:

```text
You request a package
        │
        ▼
      APT
        │
        ▼
Find dependencies
        │
        ▼
Download required packages
        │
        ▼
Install packages
        │
        ▼
Software becomes available
```

---

# 18. Conclusion

Ubuntu package management provides a convenient way to install, update, remove, and manage software.

The most important tools are:

```text
APT
 ↓
High-level package management

dpkg
 ↓
Low-level .deb package management
```

Some of the most useful commands to remember are:

```bash
sudo apt update
sudo apt upgrade
sudo apt install package
sudo apt remove package
sudo apt purge package
sudo apt autoremove
apt search package
apt show package
apt list --installed
apt list --upgradable
apt policy package
dpkg -l
```

The basic workflow for keeping an Ubuntu system up to date is:

```bash
sudo apt update
sudo apt upgrade
```

For installing software:

```bash
sudo apt install package
```

For removing software:

```bash
sudo apt remove package
```

For completely removing a package and its package-managed configuration:

```bash
sudo apt purge package
```

Understanding **APT, dpkg, repositories, packages, dependencies, installation, removal, and upgrades** gives you the foundation needed to manage software effectively on Ubuntu Linux.
