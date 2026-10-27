[[Inbox]]
SatH, this is a really good Ubuntu lab project because you'll learn more than just how to copy files. You'll practise Linux commands, permissions, shell scripting, logging, scheduling, troubleshooting, and service management — exactly the sort of skills that are useful for Linux administration and infrastructure engineering.

Let's build it like a real lab, starting with manual backups, then automating them with `cron`, and finally managing the backup process with `systemd`.

The important rule: don't use AI or browse the web while solving the exercises. Use the local manual pages, command help, and your own experiments. I'll give you the goals, starter files, tasks, and hints, but leave the actual solutions for you to discover.

# Ubuntu Backup Lab: Beginner to Intermediate

![What is a Cron Job in Linux?](https://images.openai.com/static-rsc-4/kNiocrSi0ackvxKnI2hUTQq77AZsdCKX7H8tfBGhpMrfmpcr4uyeD5PAmKfcLRZMpaW2OV_MJoDRkjfrQ2L7iQA7TfIAR_2_e43PMlszB5ORDGiogenLMcjYLuZUJhvcIAbihglQHmHZCLeCyxbNw1x670LLaD_m5BhrJuR2wYM?purpose=inline)

Stage 1 — Manual backups

Foundation

Learn to create backup directories, copy files, preserve metadata, create archives, and verify that your backups work.

![Automate common and boring tasks with Bash Scripts in Linux | Tom's Hardware](https://images.openai.com/static-rsc-4/IolMujVot9WmGYRzZaQvHyHNV9PuCHt38tZZfQw-BvBQVVvVicb0KJPQ9Ar3C0humwUA6XScf-K1PXE7LvTXxbhWEgu95JCugJmaE-e03s0DFTTvC33JffhDggiZkAMKu14kVOMM4TAvMJUAdSIToq16EP7Do25vW5r93xJafFU?purpose=inline)

Stage 2 — Bash backup script

Write your own reusable script with timestamps, logging, error handling, and backup verification.

![Task Automation Using Cron Jobs and Scripts in Pop!_OS](https://images.openai.com/static-rsc-4/BcQr9NQCcH80ydr69XtTIaXPU_2zpcYBAZKD-2BXfSS3lyT1ECMMhi_DjjZ_43EJBKAkOt1nQ5nUXCB3-KuuSsbRW7hHKxbYcjXRqHRTSFT6lhBbJVQm7bXXUfkPnnaBFfTdm8mfS7C-6hdk_6jHroZbS6d7O32G2GHYsJkk0Rw?purpose=inline)

Stage 3 — Cron automation

Schedule backups, inspect logs, test the schedule, and troubleshoot failed jobs.

![Managing services with systemctl in Linux - SysOpsPro](https://images.openai.com/static-rsc-4/01VfpefNVHmFcyOGckpWdTeGeN_DWUo26qDYXUmM-2vC28U0543CnQFcaodbsj6yGWsjxKrCqBjhDa5b5OglwWqKZIaTMyZl72KxVsY9CvPs4ZlN2RgTIBvwDZbUl-HyP2Rt20Nm9Aur2SwYXaoN35pXoVx_nUkR0fk-fbNPxG0?purpose=inline)

Stage 4 — systemd automation

Create a oneshot service and a timer, inspect service status, examine journal logs, and compare systemd timers with cron.

## 1. Create your lab environment

Use a dedicated directory under your home folder. This keeps your practice files separate from real personal files and makes testing much safer.

Run these commands yourself:

```
mkdir -p ~/backup-lab/{data,backup,logs,scripts,restore}
cd ~/backup-lab
```

Explore what you created:

```
pwd
ls -lah
find . -maxdepth 2 -type d
```

Now investigate each command rather than blindly accepting it. For example:

```
man mkdir
man ls
man find
```

Your first mini-goal is to explain to yourself what `-p`, `-lah`, and `-maxdepth 2` mean.

I've also prepared a small set of practice files for your lab.

Ubuntu Backup Lab — Starter Files

ZIP archive · Sample notes, fake configurations, reports, and a CSV file

Download the starter files

Extract the archive, then copy its `sample-data` contents into `~/backup-lab/data/`.

For example, if the archive is in your Downloads directory:

```
cd ~/backup-lab
unzip ~/Downloads/ubuntu-backup-lab-starter.zip
cp -a ubuntu-backup-lab-starter/sample-data/. data/
```

Check the resulting directory:

```
find ~/backup-lab/data -type f
```

## 2. Learn to find answers using Linux documentation

This is one of the most important parts of the exercise. When you encounter a command you don't know, follow this process instead of guessing.

Step 1: Check whether the command exists

```
command -v tar
type tar
```

These help you identify a command and, in the case of `type`, whether it is an alias, shell function, builtin, or executable.

Step 2: Read its manual

```
man tar
```

Learn how to navigate the manual using `/` to search, `n` to find the next match, and `q` to quit.

Step 3: Search for relevant documentation

```
man -k backup
apropos archive
whatis tar
```

Find commands by topic, discover relevant manual pages, and get short descriptions.

Step 4: Inspect available options

```
tar --help
cp --help
systemctl --help
```

Use this for a quick overview, then read the full manual when you need more detail.

### Your documentation challenge

Before using each tool, find the relevant manual and answer these questions in your own notes.

|Command|Questions to answer|
|---|---|
|`cp`|How do you copy a directory recursively and preserve metadata?|
|`tar`|How do you create an archive, list it, and extract it?|
|`gzip`|How do you compress and decompress files?|
|`diff`|How do you compare two text files?|
|`cmp`|How do you check whether two files are identical?|
|`find`|How do you find files by name or modification time?|
|`date`|How do you format a timestamp for a filename?|
|`du`|How do you inspect the size of directories?|
|`crontab`|How do you edit and inspect your own scheduled jobs?|
|`systemctl`|How do you inspect, start, stop, and debug services and timers?|
|`journalctl`|How do you inspect service logs?|

Pro tip: If a manual page is too long, search inside it. For example, open `man tar`, then type `/Examples` and press Enter. If that heading doesn't exist, search for another keyword such as `/create` or `/extract`.

## 3. Stage 1 — Manual backup challenge

Goal: back up the sample data without writing a script.

First, inspect the source files and their sizes. Then complete these tasks independently.

### Stage 1 checklist

0/9

Inspect all sample files and directories.

Create a separate directory for the first backup.

Copy the sample data while preserving relevant file metadata.

Create a compressed archive of the sample data.

Include a timestamp in the archive filename.

List the archive contents without extracting it.

Restore the archive into the restore directory.

Compare the restored files with the original files.

Record the archive size and the final result in your notes.

Use the following local documentation as needed:

```
man cp
man tar
man gzip
man diff
man cmp
man stat
man du
```

Questions you must answer before moving on:

- What is the difference between a regular file copy and a compressed archive?
    
- Why should you test the restore instead of merely checking that an archive exists?
    
- What happens if you create two backups with the same filename?
    
- What happens to file permissions and timestamps when you copy files using different options?
    

Don't delete your first backup to make room for the next one. Use a new filename or directory so you can compare versions.

## 4. Stage 2 — Write your own Bash backup script

Now create a script at:

```
~/backup-lab/scripts/backup.sh
```

Your task is to design it yourself. Do not copy a finished script from the internet.

The script should eventually perform these operations:

1. Define the source, backup, and log directories.
    
2. Check whether the source directory exists.
    
3. Create a unique timestamped backup filename.
    
4. Create the compressed archive.
    
5. Check the exit status of the backup operation.
    
6. Log success or failure with a timestamp.
    
7. Verify that the archive exists and is readable.
    
8. Return an appropriate exit code.
    
9. Avoid silently overwriting an existing backup.
    

Start by learning the Bash features you'll need:

```
man bash
```

Inside the Bash manual, search for `Conditional Constructs`, `Shell Expansions`, `Exit Status`, and `Arrays`.

You can also inspect the following commands:

```
man test
man date
man mkdir
man printf
man tee
```

When your first version works, test it using a deliberately invalid source directory. Does it report the failure? Does it return a nonzero exit status?

Then investigate how to check your script for common errors:

```
bash -n ~/backup-lab/scripts/backup.sh
```

Read `man bash` to understand what this check does and, importantly, what it does not do.

## 5. Stage 3 — Automate the backup with cron

Goal: run your working backup script automatically.

First, read the documentation:

```
man cron
man crontab
```

On Ubuntu, you can also inspect the installed cron documentation with:

```
cron --help
```

Then investigate how to:

- Open your personal crontab for editing.
    
- List existing scheduled jobs.
    
- Schedule a job to run every five minutes temporarily for testing.
    
- Redirect standard output and standard error to a log.
    
- Check whether the cron service is running.
    
- Remove your temporary schedule when testing is complete.
    

Useful local references:

```
man 5 crontab
man 8 cron
man systemctl
```

For your lab, keep the schedule temporary and use only your test data. Don't schedule frequent backups of your entire home directory.

### Cron troubleshooting exercise

Intentionally make a test run fail, then investigate why.

Ask yourself:

- Does cron use the same environment as my interactive terminal?
    
- Is the script executable?
    
- Does the script use absolute paths?
    
- Where does its output go?
    
- How can I distinguish a cron scheduling problem from a script failure?
    

A good backup script should work when launched manually before you try to schedule it.

## 6. Stage 4 — Move from cron to systemd

Now you'll manage the same backup script using a systemd service and timer.

For this lab, use a systemd user service so you can practise without installing a system-wide service or running the backup as root.

Read these manual pages first:

```
man systemd
man systemd.service
man systemd.timer
man systemctl
man journalctl
```

Your goal is to create these two files:

```
~/.config/systemd/user/backup-lab.service
~/.config/systemd/user/backup-lab.timer
```

The service should:

- Run your backup script.
    
- Execute the backup as your regular user.
    
- Be a oneshot service rather than a permanently running process.
    
- Return a meaningful success or failure status.
    

The timer should:

- Start the service on a schedule.
    
- Use a short interval for testing.
    
- Demonstrate how a timer can be configured to run after a delay.
    
- Be enabled and inspected independently from the service.
    

Learn the following commands from their manuals before using them:

```
systemctl --user daemon-reload
systemctl --user status backup-lab.service
systemctl --user list-timers
journalctl --user -u backup-lab.service
```

The commands above are examples of the operations to learn, not a complete installation procedure. You'll still need to create the unit files with valid contents.

One important distinction: a user timer generally runs while your user systemd manager is active. Running jobs while logged out may require additional configuration, such as lingering. Investigate `man loginctl` and the `enable-linger` option before deciding whether you need that behaviour.

### Final systemd challenge

Make your service run manually first, then activate its timer. Verify the following:

- The service starts successfully.
    
- The archive is created.
    
- The journal contains the expected result.
    
- A deliberately failed backup is reported correctly.
    
- Repeated runs do not overwrite previous archives.
    
- You can stop and disable the timer when the exercise is finished.
    

## 7. Final assessment

When you've completed all four stages, test yourself with these scenarios.

### Practical exam

0/6

Scenario 1 — Missing source

Rename the lab data directory and run the backup script. Does it fail safely?

Scenario 2 — Permission denied

Use a disposable file with restricted permissions. Determine how the script responds when it cannot read the source.

Scenario 3 — Corrupt archive

Make a copy of an archive, damage the copy, and investigate how to detect the problem without risking the original.

Scenario 4 — Duplicate runs

Run the backup twice in quick succession. Check whether the filenames collide.

Scenario 5 — Scheduler failure

Disable or misconfigure a test schedule, diagnose the issue, and restore the correct configuration.

Scenario 6 — Recovery

Restore a previous backup into an empty directory and verify the recovered contents.

One last habit: after every experiment, write down the command you used, what you expected, what actually happened, and the relevant manual page. That turns your lab into a useful Obsidian knowledge base rather than a collection of commands you've forgotten.

Your first task now: download the starter files, set up `~/backup-lab`, and complete Stage 1 without writing any scripts. When you're done, show me the commands you tried and their output. I'll review your approach and help you troubleshoot without taking away the learning experience.