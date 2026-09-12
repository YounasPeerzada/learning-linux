# Linux Networking

Linux networking is the set of tools, commands, and concepts used to configure, inspect, troubleshoot, and manage network connections on a Linux system.

---

# Table of Contents

1. [What is Networking?](#1-what-is-networking)
2. [Network Interface](#2-network-interface)
3. [IP Address](#3-ip-address)
4. [IPv4 vs IPv6](#4-ipv4-vs-ipv6)
5. [MAC Address](#5-mac-address)
6. [Network Interface Commands](#6-network-interface-commands)
7. [IP Command](#7-ip-command)
8. [Routing](#8-routing)
9. [Routing Table](#9-routing-table)
10. [Default Gateway](#10-default-gateway)
11. [DNS](#11-dns)
12. [DNS Commands](#12-dns-commands)
13. [Hostname](#13-hostname)
14. [Connectivity Testing](#14-connectivity-testing)
15. [Ports](#15-ports)
16. [TCP and UDP](#16-tcp-and-udp)
17. [Network Connections](#17-network-connections)
18. [Network Statistics](#18-network-statistics)
19. [ARP and Neighbor Table](#19-arp-and-neighbor-table)
20. [DHCP](#20-dhcp)
21. [Network Configuration Files](#21-network-configuration-files)
22. [SSH Networking](#22-ssh-networking)
23. [Firewall](#23-firewall)
24. [Network Troubleshooting](#24-network-troubleshooting)
25. [Useful Networking Commands](#25-useful-networking-commands)

---

# 1. What is Networking?

Networking allows computers to communicate with each other.

### Important terms

* **Network:** Connected devices communicating with each other.
* **Client:** Device that requests a service.
* **Server:** Device that provides a service.
* **LAN:** Local Area Network.
* **WAN:** Wide Area Network.
* **Internet:** Global network of interconnected networks.

Basic communication:

```text
Client → Network → Router → Internet → Server
```

---

# 2. Network Interface

A **network interface** allows Linux to communicate with a network.

Common interfaces:

```text
eth0     → Ethernet
ens33    → Ethernet
wlan0    → Wi-Fi
lo       → Loopback
```

The loopback interface normally uses:

```text
127.0.0.1
```

Check interfaces:

```bash
ip link
```

---

# 3. IP Address

An IP address identifies a device on a network.

Example:

```text
192.168.1.10
```

### Private IPv4 ranges

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

Check your IP:

```bash
ip addr
```

Short form:

```bash
ip a
```

---

# 4. IPv4 vs IPv6

### IPv4

Uses 32-bit addresses.

Example:

```text
192.168.1.10
```

### IPv6

Uses 128-bit addresses.

Example:

```text
2001:db8::1
```

Check both:

```bash
ip addr
```

Force IPv4:

```bash
ping -4 google.com
```

Force IPv6:

```bash
ping -6 google.com
```

---

# 5. MAC Address

A **MAC address** identifies a network interface at the hardware/data-link level.

Example:

```text
00:1A:2B:3C:4D:5E
```

Check MAC addresses:

```bash
ip link
```

Example output:

```text
link/ether 00:1A:2B:3C:4D:5E
```

---

# 6. Network Interface Commands

View interfaces:

```bash
ip link
```

View IP addresses:

```bash
ip addr
```

Bring interface up:

```bash
sudo ip link set eth0 up
```

Bring interface down:

```bash
sudo ip link set eth0 down
```

Show a specific interface:

```bash
ip addr show eth0
```

---

# 7. IP Command

`ip` is the modern Linux command for network configuration and inspection.

### View addresses

```bash
ip addr
```

### View interfaces

```bash
ip link
```

### Add an IP address

```bash
sudo ip addr add 192.168.1.50/24 dev eth0
```

### Remove an IP address

```bash
sudo ip addr del 192.168.1.50/24 dev eth0
```

### Show routes

```bash
ip route
```

---

# 8. Routing

**Routing** determines where network packets should go.

Example:

```text
Computer
   ↓
Router
   ↓
Internet
   ↓
Server
```

Linux uses a **routing table** to make these decisions.

View routes:

```bash
ip route
```

---

# 9. Routing Table

Display the routing table:

```bash
ip route
```

Example:

```text
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0
```

Meaning:

```text
default → send unknown networks to gateway
via     → gateway address
dev     → network interface
```

Add a route:

```bash
sudo ip route add 10.0.0.0/24 via 192.168.1.1
```

Delete a route:

```bash
sudo ip route del 10.0.0.0/24
```

---

# 10. Default Gateway

The **default gateway** is the router used when Linux does not have a more specific route.

Check it:

```bash
ip route
```

Look for:

```text
default via 192.168.1.1
```

Test the gateway:

```bash
ping 192.168.1.1
```

---

# 11. DNS

**DNS (Domain Name System)** converts domain names into IP addresses.

Example:

```text
google.com
     ↓
142.250.x.x
```

Without DNS, you would need to remember IP addresses instead of domain names.

Common DNS configuration locations include:

```text
/etc/resolv.conf
```

Check DNS configuration:

```bash
cat /etc/resolv.conf
```

---

# 12. DNS Commands

Use `dig`:

```bash
dig google.com
```

Short answer:

```bash
dig +short google.com
```

Use `nslookup`:

```bash
nslookup google.com
```

Use `getent`:

```bash
getent hosts google.com
```

These commands help determine whether DNS resolution is working.

---

# 13. Hostname

A **hostname** is the name assigned to a computer.

Show hostname:

```bash
hostname
```

Detailed information:

```bash
hostnamectl
```

Change hostname:

```bash
sudo hostnamectl set-hostname my-server
```

Check local hostname mappings:

```bash
cat /etc/hosts
```

---

# 14. Connectivity Testing

### Ping

Tests whether a host is reachable:

```bash
ping google.com
```

Send only 4 packets:

```bash
ping -c 4 google.com
```

### Trace route

Shows the path packets take:

```bash
traceroute google.com
```

If `traceroute` is unavailable:

```bash
tracepath google.com
```

---

# 15. Ports

A **port** identifies a network service running on a machine.

Common ports:

| Port | Service                |
| ---: | ---------------------- |
|   22 | SSH                    |
|   53 | DNS                    |
|   80 | HTTP                   |
|  443 | HTTPS                  |
| 3306 | MySQL                  |
| 5432 | PostgreSQL             |
| 6379 | Redis                  |
| 8080 | Common web application |

Example:

```text
192.168.1.10:22
```

Here:

```text
IP address → 192.168.1.10
Port       → 22
```

---

# 16. TCP and UDP

### TCP

TCP is connection-oriented and provides reliable delivery.

Used by:

```text
HTTP/HTTPS
SSH
FTP
```

### UDP

UDP is connectionless and has lower overhead.

Common uses:

```text
DNS
DHCP
Streaming
VoIP
```

Basic difference:

```text
TCP → Reliable
UDP → Faster/lower overhead
```

---

# 17. Network Connections

Modern Linux systems commonly use `ss` to inspect sockets and connections.

Show listening ports:

```bash
ss -tuln
```

Show TCP connections:

```bash
ss -t
```

Show UDP connections:

```bash
ss -u
```

Show processes using network connections:

```bash
sudo ss -tulpn
```

Useful for finding which service is listening on a port.

---

# 18. Network Statistics

View network statistics:

```bash
ss -s
```

View interface statistics:

```bash
ip -s link
```

Useful information includes:

```text
RX packets
TX packets
errors
dropped packets
```

These can help identify network problems.

---

# 19. ARP and Neighbor Table

ARP maps an IPv4 address to a MAC address on the local network.

Modern Linux uses the neighbor table.

View it:

```bash
ip neigh
```

Example:

```text
192.168.1.1 dev eth0 lladdr aa:bb:cc:dd:ee:ff REACHABLE
```

Here:

```text
IP address → 192.168.1.1
MAC        → aa:bb:cc:dd:ee:ff
```

---

# 20. DHCP

**DHCP (Dynamic Host Configuration Protocol)** automatically provides network configuration.

It can provide:

```text
IP address
Subnet mask
Default gateway
DNS server
```

Typical process:

```text
Client
  ↓
DHCP Discover
  ↓
DHCP Offer
  ↓
DHCP Request
  ↓
DHCP ACK
```

Check your assigned address:

```bash
ip addr
```

On systems using NetworkManager:

```bash
nmcli device show
```

---

# 21. Network Configuration Files

Important Linux networking files:

### `/etc/hosts`

Maps hostnames to IP addresses.

```bash
cat /etc/hosts
```

Example:

```text
127.0.0.1 localhost
192.168.1.20 myserver
```

### `/etc/resolv.conf`

Contains DNS resolver configuration.

```bash
cat /etc/resolv.conf
```

### NetworkManager

Check network devices:

```bash
nmcli device
```

Show connections:

```bash
nmcli connection show
```

---

# 22. SSH Networking

**SSH (Secure Shell)** allows remote access to another Linux machine.

Connect:

```bash
ssh username@server-ip
```

Example:

```bash
ssh ubuntu@192.168.1.20
```

Specify a port:

```bash
ssh -p 2222 username@server-ip
```

Copy a file:

```bash
scp file.txt username@server-ip:/home/username/
```

Test whether SSH is listening:

```bash
ss -tln | grep :22
```

---

# 23. Firewall

A firewall controls incoming and outgoing network traffic.

Common Linux firewall tools:

```text
ufw
firewalld
nftables
iptables
```

### UFW

Check status:

```bash
sudo ufw status
```

Allow SSH:

```bash
sudo ufw allow 22/tcp
```

Allow HTTP:

```bash
sudo ufw allow 80/tcp
```

Allow HTTPS:

```bash
sudo ufw allow 443/tcp
```

Enable firewall:

```bash
sudo ufw enable
```

> Always allow SSH before enabling a firewall on a remote server, otherwise you may lock yourself out.

---

# 24. Network Troubleshooting

When networking does not work, troubleshoot layer by layer.

### Step 1 — Check interface

```bash
ip link
```

### Step 2 — Check IP address

```bash
ip addr
```

### Step 3 — Check gateway

```bash
ip route
```

### Step 4 — Ping gateway

```bash
ping 192.168.1.1
```

### Step 5 — Test Internet

```bash
ping 8.8.8.8
```

### Step 6 — Test DNS

```bash
ping google.com
```

If IP works but domain does not:

```text
Network works
      ↓
DNS problem
```

### Step 7 — Check ports

```bash
ss -tuln
```

### Step 8 — Check firewall

```bash
sudo ufw status
```

---

# 25. Useful Networking Commands

| Command       | Purpose                  |
| ------------- | ------------------------ |
| `ip addr`     | Show IP addresses        |
| `ip link`     | Show network interfaces  |
| `ip route`    | Show routing table       |
| `ip neigh`    | Show neighbor/ARP table  |
| `ping`        | Test connectivity        |
| `ss`          | Show sockets/connections |
| `dig`         | Query DNS                |
| `nslookup`    | Query DNS                |
| `hostname`    | Show hostname            |
| `hostnamectl` | Manage hostname          |
| `traceroute`  | Trace network path       |
| `tracepath`   | Trace network path       |
| `nmcli`       | Manage NetworkManager    |
| `scp`         | Copy files over SSH      |
| `ssh`         | Remote login             |
| `ufw`         | Manage firewall          |
| `curl`        | Test HTTP/HTTPS          |
| `wget`        | Download resources       |
| `ethtool`     | Inspect Ethernet devices |
| `tcpdump`     | Capture network packets  |

### `curl`

Test a website:

```bash
curl https://example.com
```

Show HTTP headers:

```bash
curl -I https://example.com
```

### `wget`

Download a file:

```bash
wget https://example.com/file.zip
```

### `ethtool`

View interface information:

```bash
sudo ethtool eth0
```

### `tcpdump`

Capture packets:

```bash
sudo tcpdump -i eth0
```

Capture traffic for a specific port:

```bash
sudo tcpdump -i eth0 port 80
```

---

# Quick Linux Networking Workflow

When working with a Linux server, remember this order:

```text
1. Interface
      ↓
2. IP Address
      ↓
3. Routing
      ↓
4. Gateway
      ↓
5. DNS
      ↓
6. Ports
      ↓
7. Services
      ↓
8. Firewall
```

Useful first commands:

```bash
ip addr
ip route
ip link
ip neigh
ss -tuln
ping 8.8.8.8
ping google.com
cat /etc/resolv.conf
sudo ufw status
```

## Conclusion

The most important Linux networking tools to learn first are:

```text
ip
ss
ping
dig
ip route
ip neigh
nmcli
ssh
scp
curl
tcpdump
ufw
```

These commands cover most everyday tasks involving **network configuration, connectivity testing, remote servers, ports, DNS, routing, SSH, and troubleshooting**.
