# Linux Processes and Process Management

## Table of Contents

1. [What is a Process?](#1-what-is-a-process)
2. [Program vs Process](#2-program-vs-process)
3. [Process States](#3-process-states)
4. [Process ID (PID)](#4-process-id-pid)
5. [Parent and Child Processes](#5-parent-and-child-processes)
6. [Process Tree](#6-process-tree)
7. [Foreground and Background Processes](#7-foreground-and-background-processes)
8. [Starting Processes](#8-starting-processes)
9. [Viewing Processes](#9-viewing-processes)
10. [ps Command](#10-ps-command)
11. [top Command](#11-top-command)
12. [htop Command](#12-htop-command)
13. [btop Command](#13-btop-command)
14. [pgrep Command](#14-pgrep-command)
15. [pidof Command](#15-pidof-command)
16. [Killing and Terminating Processes](#16-killing-and-terminating-processes)
17. [kill Command](#17-kill-command)
18. [killall Command](#18-killall-command)
19. [pkill Command](#19-pkill-command)
20. [Signals](#20-signals)
21. [Common Linux Signals](#21-common-linux-signals)
22. [Process Priority](#22-process-priority)
23. [nice Command](#23-nice-command)
24. [renice Command](#24-renice-command)
25. [CPU and Memory Usage](#25-cpu-and-memory-usage)

---

## 1. What is a Process?

A process is a running instance of a program.
For example, when you run:

```bash
python3 app.py
```

the file `app.py` is a program, but once Linux starts executing it, it becomes a process.
A process has resources such as:

- Process ID (PID)
- Memory
- CPU time
- Open files
- Environment variables
- Security permissions
- Process state
- Priority

```text
Program
   |
   | Execute
   ↓
Process
   |
   ├── PID
   ├── Memory
   ├── CPU
   ├── Files
   └── State
```

---

## 2. Program vs Process

| Program               | Process               |
| --------------------- | --------------------- |
| Passive               | Active                |
| Stored on disk        | Running in memory     |
| Contains instructions | Executes instructions |
| Does not have a PID   | Has a PID             |
| Example: `/bin/ls`    | Running `ls`          |

Think of a program as a recipe and a process as someone currently cooking that recipe.

---

## 3. Process States

Linux processes can have different states.

| State | Meaning               |
| ----- | --------------------- |
| **R** | Running or runnable   |
| **S** | Interruptible sleep   |
| **D** | Uninterruptible sleep |
| **T** | Stopped               |
| **Z** | Zombie                |
| **I** | Idle kernel thread    |

You can see process states with:

```bash
ps aux
```

or:

```bash
ps -ef
```

---

## 4. Process ID (PID)

Every running process normally receives a unique Process ID, called a PID.

Example command:

```bash
ps
```

Output may look like:

```text
PID TTY          TIME CMD
1250 pts/0    00:00:00 bash
1820 pts/0    00:00:00 python3
```

Here:

- `1250` → PID of `bash`
- `1820` → PID of `python3`

You can find the PID of a process with:

```bash
pgrep python3
```

---

## 5. Parent and Child Processes

Processes can create other processes.
The process that creates another process is called the parent process.
The newly created process is called the child process.

```text
Parent Process
      |
      ├── Child Process 1
      |
      ├── Child Process 2
      |
      └── Child Process 3
```

You can view parent and child relationships with:

```bash
ps -ef
```

Important columns include:

- **PID:** Process ID
- **PPID:** Parent Process ID

Example:

```text
UID   PID   PPID   CMD
user  2000  1500   bash
user  2100  2000   python3
```

Here:
`python3` → Parent = `bash`

---

## 6. Process Tree

A process tree shows parent-child relationships.

Use:

```bash
pstree
```

Example output:

```text
systemd
 ├─ sshd
 │   └─ bash
 │       └─ python3
 ├─ NetworkManager
 └─ cron
```

You can also use:

```bash
pstree -p
```

to show PIDs.

---

## 7. Foreground and Background Processes

### Foreground Process

A foreground process occupies your terminal.

Example:

```bash
ping google.com
```

Your terminal remains attached to the command. Press `Ctrl + C` to terminate it.

### Background Process

A background process runs without occupying the terminal.

Example:

```bash
ping google.com &
```

The `&` starts the command in the background.

Example output:

```text
[1] 2450
```

- `1` = Job ID
- `2450` = PID

---

## 8. Starting Processes

Running a command starts a process.

Example:

```bash
sleep 100
```

Another example:

```bash
python3 app.py
```

You can run it in the background:

```bash
python3 app.py &
```

---

## 9. Viewing Processes

Linux provides several commands for monitoring processes.

Important commands:

- `ps`
- `top`
- `htop`
- `pgrep`
- `pidof`

---

## 10. ps Command

`ps` means Process Status.

Basic usage:

```bash
ps
```

Show all processes for all users:

```bash
ps aux
```

Another common format:

```bash
ps -ef
```

### Useful examples

- Show your processes:
  ```bash
  ps
  ```
- Show all processes:
  ```bash
  ps aux
  ```
- Show a specific PID:
  ```bash
  ps -p 1234
  ```
- Show process hierarchy:
  ```bash
  ps --forest
  ```

---

## 11. top Command

`top` provides a real-time view of running processes.

Run:

```bash
top
```

It displays information such as:

- CPU usage
- Memory usage
- PID
- Process owner
- Process priority
- Running processes

Example:

```text
PID   USER   %CPU   %MEM   COMMAND
1200  user   25.4   4.2    python3
1500  user   10.2   2.1    chrome
```

Press `q` to exit.

---

## 12. htop Command

`htop` is an interactive alternative to `top`.

Run:

```bash
htop
```

It provides a more user-friendly interface. If it is not installed:

```bash
sudo apt install htop
```

---

## 13. btop Command

`btop` (or `btop++`) is a modern, responsive, and visual resource monitor that tracks CPU, memory, disks, network, and process usage.

Run:

```bash
btop
```

It offers a rich terminal UI with graphs, mouse support, and dynamic process filtering. If it is not installed:

```bash
sudo apt install btop
```

---

## 14. pgrep Command

`pgrep` searches for processes by name.

Example:

```bash
pgrep python3
```

You can get both PID and name:

```bash
pgrep -a python3
```

Output:

```text
1820 python3 app.py
```

---

## 15. pidof Command

`pidof` finds the PID of a running program.

Example:

```bash
pidof bash
```

Output:

```text
1250
```

For multiple instances:

```bash
pidof python3
```

might return:

```text
1820 1900 2050
```

---

## 16. Killing and Terminating Processes

Sometimes a process needs to be stopped.
For example:

- Program is frozen
- Application is consuming too much CPU
- Server needs to be stopped
- Process is no longer needed

Linux provides signals to control processes.

---

## 17. kill Command

Despite its name, `kill` does not necessarily mean immediately destroying a process. It sends a signal to a process.

Basic syntax:

```bash
kill PID
```

Example:

```bash
kill 1820
```

By default, this sends `SIGTERM`. You can explicitly specify it:

```bash
kill -SIGTERM 1820
```

or:

```bash
kill -15 1820
```

---

## 18. killall Command

`killall` sends a signal to processes by name.

Example:

```bash
killall firefox
```

This can affect multiple processes with that name. Use it carefully.

---

## 19. pkill Command

`pkill` also allows you to terminate processes by name or other matching criteria.

Example:

```bash
pkill python3
```

You can also use:

```bash
pkill -f "app.py"
```

---

## 20. Signals

A signal is a software notification sent to a process.
Signals allow Linux or another process to tell a process to perform an action.

Examples:

- `SIGTERM`
- `SIGKILL`
- `SIGSTOP`
- `SIGCONT`
- `SIGHUP`
- `SIGINT`

---

## 21. Common Linux Signals

| Signal      | Number | Purpose                  |
| ----------- | ------ | ------------------------ |
| **SIGHUP**  | 1      | Hangup                   |
| **SIGINT**  | 2      | Interrupt                |
| **SIGQUIT** | 3      | Quit                     |
| **SIGKILL** | 9      | Forcefully terminate     |
| **SIGTERM** | 15     | Request termination      |
| **SIGSTOP** | 19     | Stop process             |
| **SIGCONT** | 18     | Continue stopped process |

### SIGTERM

```bash
kill -15 PID
```

Requests that the process terminate gracefully.

### SIGKILL

```bash
kill -9 PID
```

Immediately terminates the process. Use `SIGKILL` only when necessary because the process cannot clean up normally.

### SIGSTOP

```bash
kill -STOP PID
```

Stops a process.

### SIGCONT

```bash
kill -CONT PID
```

Continues a stopped process.

---

## 22. Process Priority

Linux assigns processes a scheduling priority.
A concept closely related to this is the **nice value**.

Nice values generally range from:

- `-20` → highest priority
- `0` → default
- `+19` → lowest priority

> **Important:**
>
> - Lower nice value = higher CPU scheduling priority
> - Higher nice value = lower CPU scheduling priority

---

## 23. nice Command

Start a process with a specific nice value.

Example:

```bash
nice -n 10 ./program
```

This starts the program with a nice value of 10. For a CPU-intensive background task, you might use a positive nice value so it is less favored by the scheduler.

---

## 24. renice Command

Change the nice value of an existing process.

Example:

```bash
renice 10 -p 1234
```

This changes PID 1234 to nice value 10.

Increasing priority usually requires appropriate privileges:

```bash
sudo renice -5 -p 1234
```

---

## 25. CPU and Memory Usage

Process management also involves monitoring resource usage.

Using commands like `top`, `htop`, or `btop`, you can monitor:

- `%CPU`
- `%MEM`

Example readout:

```text
PID    %CPU    %MEM    COMMAND
2000   85.4    2.1     python3
2100   10.2    5.4     chrome
```

- A process using a very high percentage of CPU may be CPU-intensive.
- A process using a large percentage of memory may be memory-intensive.
