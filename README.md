# Linux-Archiving-and-Logging-Data

## Week 5 Homework Submission File: Archiving and Logging Data

Please edit this file by adding the solution commands on the line below the prompt.

Save and submit the completed file for your homework submission.

---

### Step 1: Create, Extract, Compress, and Manage tar Backup Archives

1. Command to **extract** the `TarDocs.tar` archive to the current directory:

tar xvvf TarDocs.tar

2. Command to **create** the `Javaless_Doc.tar` archive from the `TarDocs/` directory, while excluding the `TarDocs/Documents/Java` directory:
tar -zcv --exclude='TarDocs/Documents/Java' -f Javaless_Doc.tar TarDocs

3. Command to ensure `Java/` is not in the new `Javaless_Docs.tar` archive:
tar tvf Javaless_Doc.tar | grep /java/

**Bonus** tar
- Command to create an incremental archive called `logs_backup.tar.gz` with only changed files to `snapshot.file` for the `/var/log` directory:

sudo tar cvvWf logs_backup_mon.tar.gz --listed-incremental=logs_backup.snar --level=0 /var/log

#### Critical Analysis Question

- Why wouldn't you use the options `-x` and `-c` at the same time with `tar`?
-x extracts a tar and -c creates a tar


### Step 2: Create, Manage, and Automate Cron Jobs

1. Cron job for backing up the `/var/log/auth.log` file:

0 6 * * 3 tar -cvf - ./var/log/auth.log > auth_backup.tgz >/dev/null 2>&1

### Step 3: Write Basic Bash Scripts

1. Brace expansion command to create the four subdirectories

sudo mkdir /backups/{freemem,diskuse,openlist,freedisk}

2. Paste your `system.sh` script edits below:
#!/bin/bash
echo "Free Memory"
sudo free -h
sudo free -h >> ~/backups/freemem/free_mem.txt
echo "Disk Usage"
sudo du -h
sudo du -h >> ~/backups/diskuse/disk_usage.txt
echo "Open Files"
sudo lsof
sudo lsof >> ~/backups/openlist/open_list.txt
echo "Disk Space Statistics"
sudo df -h
sudo df -h >> ~/backups/freedisk/free_disk.txt
                                                

3. Command to make the `system.sh` script executable:
Chmod +x
**Optional**
- Commands to test the script and confirm its execution:
sudo ./system.sh
**Bonus**
- Command to copy `system` to system-wide cron directory:

sudo mv system.sh /etc/cron.weekly/system

### Step 4. Manage Log File Sizes
 
1. Run `sudo nano /etc/logrotate.conf` to edit the `logrotate` configuration file. 

    Configure a log rotation scheme that backs up authentication messages to the `/var/log/auth.log`.

    - Add your config file edits below:

    /var/log/auth.log {
    missingok
    weekly
    rotate 7
    notifempty
    compress
    delaycompress
}


### Bonus: Check for Policy and File Violations

1. Command to verify `auditd` is active:

systemctl status auditd

2. Command to set number of retained logs and maximum log file size:

    - Add the edits made to the configuration file below:

    max_log_file = 35
    num_logs = 7

3. Command using `auditd` to set rules for `/etc/shadow`, `/etc/passwd` and `/var/log/auth.log`:


    - Add the edits made to the `rules` file below:

    -w /etc/shadow -p wra -k hashpass_audit
    -w /etc/passwd -p wra -k userpass_audit
    -w /var/log/auth.log -p wra -k authlog_audit

4. Command to restart `auditd`:

sudo service auditd restart

5. Command to list all `auditd` rules:

sudo auditctl -l

6. Command to produce an audit report:

sudo aureport -au

7. Create a user with `sudo useradd attacker` and produce an audit report that lists account modifications:

sudo aureport -m

8. Command to use `auditd` to watch `/var/log/cron`:

sudo auditctl -w /var/log/cron -k cron_watch


9. Command to verify `auditd` rules:

sudo auditctl -l -s


### Bonus (Research Activity): Perform Various Log Filtering Techniques

1. Command to return `journalctl` messages with priorities from emergency to error:

journalctl -p err -b

2. Command to check the disk usage of the system journal unit since the most recent boot:

journalctl -u systemd-journald | less

3. Comand to remove all archived journal files except the most recent two:

sudo journalctl --vacuum-files=2

4. Command to filter all log messages with priority levels between zero and two, and save output to `/home/sysadmin/Priority_High.txt`:

journalctl -p 0..2 >> /home/sysadmin/Priority_High.txt

5. Command to automate the last command in a daily cronjob. Add the edits made to the crontab file below:

0 6 * * * journalctl -p 0..2 >> /home/sysadmin/Priority_High.txt >/dev/null 2>&1

---
Â© 2022 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved. 
