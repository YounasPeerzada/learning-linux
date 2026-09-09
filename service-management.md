# Linux Services & systemd

## Table of Contents

1. [What Are Linux Services?](#1-what-are-linux-services)
2. [What Is systemd?](#2-what-is-systemd)
3. [systemd Architecture](#3-systemd-architecture)
4. [systemd Units](#4-systemd-units)
5. [Service Units](#5-service-units)
6. [systemctl Command](#6-systemctl-command)
7. [Starting and Stopping Services](#7-starting-and-stopping-services)
8. [Enable and Disable Services](#8-enable-and-disable-services)
9. [Checking Service Status](#9-checking-service-status)
10. [Viewing Service Logs with journalctl](#10-viewing-service-logs-with-journalctl)
11. [Service Configuration Files](#11-service-configuration-files)

---

## 1. What Are Linux Services?

A service is a background program that performs a specific task without requiring continuous interaction from the user.

Examples:

* SSH server
* Web server
* Database server
* Docker daemon
* Cron scheduler
* Network manager

Common services:

```text
sshd
nginx
apache2
mysql
docker
cron
```

You can check running processes with:

```bash
ps aux
```

---

## 2. What Is systemd?

systemd is the service manager and initialization system used by many modern Linux distributions.

It is responsible for:

* Starting services during boot
* Stopping services during shutdown
* Managing background services
* Managing service dependencies
* Monitoring processes
* Managing logs through journald
* Managing system targets
* Managing sockets, mounts, timers, and other resources

Check whether systemd is running:

```bash
ps -p 1 -o comm=
```

Typical output:

```text
systemd
```

PID 1 is normally the first userspace process.

---

## 3. systemd Architecture

A simplified Linux boot flow:

```text
BIOS / UEFI
     ↓
Bootloader
     ↓
Linux Kernel
     ↓
systemd (PID 1)
     ↓
Targets
     ↓
Services
     ↓
Applications
```

For example:

```text
systemd
   │
   ├── ssh.service
   ├── nginx.service
   ├── docker.service
   └── cron.service
```

systemd manages these components according to their configuration and dependencies.

---

## 4. systemd Units

systemd manages resources using units.

Common unit types:

| Unit         | Purpose                  |
| ------------ | ------------------------ |
| `.service`   | Background services      |
| `.socket`    | IPC/network sockets      |
| `.target`    | Groups of units          |
| `.timer`     | Scheduled tasks          |
| `.mount`     | Filesystem mounts        |
| `.automount` | Automatic mounts         |
| `.path`      | Monitor filesystem paths |
| `.device`    | Devices                  |
| `.swap`      | Swap devices/files       |

List all units:

```bash
systemctl list-units
```

List all installed unit files:

```bash
systemctl list-unit-files
```

List services:

```bash
systemctl list-units --type=service
```

---

## 5. Service Units

A service unit normally ends with:

```text
.service
```

Examples:

```text
ssh.service
nginx.service
docker.service
```

You can often omit `.service`:

```bash
systemctl status nginx
```

is equivalent to:

```bash
systemctl status nginx.service
```

A service describes:

* What program should run
* Which user should run it
* When it should start
* What it depends on
* What happens if it crashes
* Environment variables
* Restart behavior

---

## 6. systemctl Command

`systemctl` is the primary command used to interact with systemd.

General syntax:

```bash
systemctl [command] [unit]
```

Examples:

```bash
systemctl status nginx
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
```

Check systemd version:

```bash
systemctl --version
```

Show systemd status:

```bash
systemctl status
```

List running services:

```bash
systemctl --type=service --state=running
```

---

## 7. Starting and Stopping Services

### Start

Start a service immediately:

```bash
sudo systemctl start nginx
```

This does not necessarily make it start automatically after reboot.

### Stop

```bash
sudo systemctl stop nginx
```

### Restart

```bash
sudo systemctl restart nginx
```

Useful after changing configuration:

```bash
sudo systemctl restart nginx
```

### Reload

Some services support configuration reload without completely stopping the process:

```bash
sudo systemctl reload nginx
```

This is generally preferable when supported because it can avoid interrupting active connections.

### Reload or restart

```bash
sudo systemctl reload-or-restart nginx
```

---

## 8. Enable and Disable Services

There is an important difference between:

```text
start
```

and:

```text
enable
```

### Start

Starts the service now.

```bash
sudo systemctl start nginx
```

### Enable

Configures the service to start automatically during boot.

```bash
sudo systemctl enable nginx
```

### Enable and start

Common command:

```bash
sudo systemctl enable --now nginx
```

This:

* Enables the service
* Starts it immediately

### Disable

Prevents automatic startup:

```bash
sudo systemctl disable nginx
```

### Disable and stop

```bash
sudo systemctl disable --now nginx
```

---

## 9. Checking Service Status

Check a service:

```bash
systemctl status nginx
```

Example:

```text
● nginx.service - A high performance web server
     Loaded: loaded
     Active: active (running)
```

Important states:

| State              | Meaning                |
| ------------------ | ---------------------- |
| `active (running)` | Service is running     |
| `inactive`         | Service is not running |
| `failed`           | Service failed         |
| `activating`       | Service is starting    |
| `deactivating`     | Service is stopping    |

Quick check:

```bash
systemctl is-active nginx
```

Check whether enabled:

```bash
systemctl is-enabled nginx
```

Check whether failed:

```bash
systemctl is-failed nginx
```

---

## 10. Viewing Service Logs with journalctl

systemd commonly uses journald for collecting logs.

The main command is:

```bash
journalctl
```

View logs:

```bash
sudo journalctl
```

View logs for a specific service:

```bash
sudo journalctl -u nginx
```

Follow logs in real time:

```bash
sudo journalctl -u nginx -f
```

This is extremely useful when troubleshooting remote servers.

### Show recent logs

```bash
sudo journalctl -u nginx -n 50
```

Show the last 50 entries.

### Logs since current boot

```bash
sudo journalctl -u nginx -b
```

### Logs from previous boot

```bash
sudo journalctl -u nginx -b -1
```

### Logs by time

```bash
sudo journalctl -u nginx --since "1 hour ago"
```

Or:

```bash
sudo journalctl -u nginx --since today
```

### Show errors

```bash
sudo journalctl -p err
```

For a service:

```bash
sudo journalctl -u nginx -p err
```

---

## 11. Service Configuration Files

systemd service definitions are usually stored in locations such as:

```text
/etc/systemd/system/
```

and distribution/package-managed units commonly live under:

```text
/usr/lib/systemd/system/
```

or:

```text
/lib/systemd/system/
```

You can find the actual unit file used by a service:

```bash
systemctl cat nginx
```

Show the path:

```bash
systemctl show -p FragmentPath nginx
```

### Common service file structure

Example:

```ini
[Unit]
Description=My Application
After=network.target

[Service]
ExecStart=/usr/bin/myapp
Restart=always
User=myuser

[Install]
WantedBy=multi-user.target
```

### `[Unit]`

Contains general information and dependencies.

Example:

```ini
[Unit]
Description=My Application
After=network.target
```

### `[Service]`

Defines how the application runs.

Example:

```ini
[Service]
ExecStart=/usr/bin/myapp
Restart=always
User=myuser
```

### `[Install]`

Defines how the service is enabled.

Example:

```ini
[Install]
WantedBy=multi-user.target
```
