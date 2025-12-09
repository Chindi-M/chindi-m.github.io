---
layout: post
title: "A Practical Guide to Working on Linux for DevOps Engineers"
date: 2025-01-04
categories: [devops, linux]
tags: [linux, bash, command-line, systemd, shell-scripting, automation, cron, server-management, tutorial, beginner-friendly, hands-on]
---


Linux is the foundation of most modern DevOps environments. You will use it to inspect logs, manage services, automate tasks, create scripts, schedule jobs and maintain servers. This guide introduces the layout of a Linux system and shows you how to carry out common operational tasks in a clear and repeatable way.

---

## 1. Understanding the Typical Linux Folder Structure

Linux uses a predictable and tidy directory layout. These are the locations you will use most often.

**`/`**  
The root directory. All files and folders branch from this point.

**`/home`**  
Contains user home directories.

Examples:
```
/home/mark
/home/admin
```

**`/etc`**  
Contains system wide configuration files. Many services place their main config files here.

**`/var`**  
Contains variable data such as logs and service data.

**`/var/log`**  
Stores system logs and application logs.

**`/usr`**  
Contains installed applications, libraries and shared resources.

**`/opt`**  
Commonly holds optional software or vendor tools.

**`/tmp`**  
Temporary files that are cleared regularly.

---

## 2. Navigating the Filesystem

Move to the root directory:

```bash
cd /
```

Move to your home directory:

```bash
cd ~
```

Move to another user's home directory:

```bash
cd /home/<username>
```

Move up one level:

```bash
cd ..
```

Show your present working directory:

```bash
pwd
```

List files in a directory:

```bash
ls -l
```

---

## 3. Viewing and Editing Files

**Read a file:**

```bash
cat filename.txt
```

**View a file page by page:**

```bash
less filename.txt
```

**Write text to a file:**

```bash
echo "Hello world" > file.txt
```

This overwrites the file. To append instead:

```bash
echo "Another line" >> file.txt
```

**View log files:**

```bash
cd /var/log
cat syslog
cat auth.log
```

For live updates:

```bash
tail -f syslog
```

---

## 4. Inspecting Running Processes

See all running processes:

```bash
ps aux
```

Search for a specific process:

```bash
ps aux | grep nginx
```

View active processes interactively:

```bash
top
```

Or a more modern view:

```bash
htop
```

(Install with: `sudo apt install htop`)

---

## 5. Starting and Stopping Services

Modern Linux systems use systemd.

**Check a service status:**

```bash
sudo systemctl status nginx
```

**Start a service:**

```bash
sudo systemctl start nginx
```

**Stop a service:**

```bash
sudo systemctl stop nginx
```

**Restart a service:**

```bash
sudo systemctl restart nginx
```

**Enable a service at startup:**

```bash
sudo systemctl enable nginx
```

---

## 6. Viewing System Logs

journald manages system logs. You can view them with:

```bash
journalctl
```

Show recent logs:

```bash
journalctl -n 50
```

Follow logs in real time:

```bash
journalctl -f
```

View logs for a specific service:

```bash
journalctl -u nginx
```

---

## 7. User Switching: sudo, su and Privileges

**Run a single command with admin rights:**

```bash
sudo command
```

**Switch to the root user:**

```bash
sudo su
```

Or:

```bash
su -
```

Return to your user:

```bash
exit
```

---

## 8. Running Shell Scripts

Mark your script as executable:

```bash
chmod +x script.sh
```

Run the script:

```bash
./script.sh
```

You can run it with Bash:

```bash
bash script.sh
```

---

## 9. Creating Simple Bash Scripts

Create a script:

```bash
nano backup.sh
```

Example script:

```bash
#!/bin/bash
echo "Starting backup"
tar -czf backup.tar.gz /home/mark/documents
echo "Backup complete"
```

Save and run:

```bash
chmod +x backup.sh
./backup.sh
```

---

## 10. Useful Automation Examples

**Clear old logs:**

```bash
#!/bin/bash
find /var/log -type f -mtime +30 -delete
```

**Check available disk space:**

```bash
#!/bin/bash
df -h
```

**Monitor memory usage:**

```bash
#!/bin/bash
free -h
```

---

## 11. Cron Jobs

Cron runs scheduled tasks.

**Edit cron jobs for your user:**

```bash
crontab -e
```

**Schedule a script to run every hour:**

```bash
0 * * * * /home/mark/backup.sh
```

**List your cron jobs:**

```bash
crontab -l
```

---

## 12. Shell Profiles and Aliases

You can customise your shell environment by editing your profile files.

**For Bash:**

```bash
nano ~/.bashrc
```

**For Zsh:**

```bash
nano ~/.zshrc
```

**Add an alias:**

Inside `.bashrc` or `.zshrc`:

```bash
alias ll="ls -l"
alias gs="git status"
alias ..="cd .."
```

**Apply the changes without logging out:**

```bash
source ~/.bashrc
```

Or:

```bash
source ~/.zshrc
```

---

## 13. Helpful Commands for Everyday Use

Show disk usage:

```bash
df -h
```

Show directory sizes:

```bash
du -sh *
```

Find files:

```bash
find / -name "filename.txt" 2>/dev/null
```

Check network connections:

```bash
netstat -tulnp
```

Check machine uptime:

```bash
uptime
```

---

## Closing Thoughts

Linux is a core skill in DevOps. The more you navigate the filesystem, manage services and automate tasks with scripts, the faster you will become familiar with real production workflows. This guide gives you the foundation you need to explore, monitor and manage Linux systems with confidence.


---
