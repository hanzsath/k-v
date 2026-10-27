[[../tags/backup]]

Basic to Advnce. Start with simple one the i wnat to go deepr, add path and automaticly upload to github.

So i want to leanr it step by step. First things i want manul do it and test it. SO make some files.

```bash
mkdir project
cd project
mkdir -p {config,data,logs,docs}

```

[[../tags/backup]]
# Introduction

You are the "Backup Sentinel," the newly appointed system administrator for a promising tech startup. A recent, minor power surge caused a server glitch, momentarily corrupting a non-critical log file. While no important data was lost this time, it was a serious wake-up call.

The CTO has personally tasked you with a critical mission: implement a robust backup and recovery strategy for the company's main application server, and do it _today_. The integrity of user data, application configurations, and vital logs is now in your hands.

This is your moment to shine. By successfully creating, verifying, and automating the backup process, you will not only safeguard the company's most valuable asset—its data—but also prove yourself as an indispensable guardian of its digital infrastructure. The system is live, the clock is ticking. Let's get to work.

Important Notice

This challenge covers skills beyond [Quick Start with Linux](https://labex.io/courses/quick-start-with-linux). If you have not learned these topics yet, skip it for now and continue to [Linux for Noobs](https://labex.io/courses/linux-for-noobs), the next course in the [Linux learning path](https://labex.io/learn/linux).

**When to return:**

1. Complete the [Scheduled Backup and Recovery](https://labex.io/labs/linux-scheduled-backup-and-recovery-725417?course=linux-for-noobs) Guided Lab, then return and try this challenge again.



Great! Let me prepare the lab VM for you. While it's being set up, you can start reviewing the first step of this lab.


# Identifying Critical Data for Backup

Before creating a backup, your first task is to identify which data is critical. A full system backup is often impractical. For our application server, the most important assets are in the `data`, `config`, and `logs` directories.

To make our backup process clean and manageable, we will create a file that lists all the directories we want to back up. This list will serve as a manifest for our backup script.

## Tasks

- Create a file named `backup-list.txt` in the `~/project` directory.
- This file should contain the relative paths to the three critical directories, with each path on a new line.

## Requirements

- The file must be named exactly `backup-list.txt`.
- The file must be located in the `~/project` directory.
- The file must contain the following three entries, each on its own line:
    - `data`
    - `config`
    - `logs`

## Examples

After creating the `backup-list.txt` file, your project directory should contain the new manifest file alongside the existing directories:

```bash
~/project/
├── backup-list.txt
├── backups/
├── config/
├── data/
└── logs/
```

When you run `cat backup-list.txt`, you should see the three critical directories listed:

```plaintext
data
config
logs
```

## Hints

- You can use a text editor like `nano` to create and edit the file.
- Alternatively, you can use the `echo` command with output redirection (`>`) to create the file and `>>` to append to it.

Continue



Inspect Result

Check if backup-list.txt exists and contains 'data'

Check if backup-list.txt contains 'config' and 'logs'

Continue


# Creating a Full System Backup Archive

With the list of critical directories ready, it's time to create the backup archive. The standard Linux tool for this job is `tar` (Tape Archive). It can bundle multiple files and directories into a single file. We'll also compress the archive to save space using `gzip`.

## Tasks

- Use the `tar` command to create a compressed backup archive.
- The archive should be named `system-backup.tar.gz`.
- The archive must be placed in the `~/project/backups/` directory.
- The contents of the archive should be determined by the `backup-list.txt` file you created in the previous step.

## Requirements

- The final archive must be located at `~/project/backups/system-backup.tar.gz`.
- You must use the `tar` command.
- You must use the `-T` option with `tar` to read the list of files/directories from `backup-list.txt`.
- The archive must be compressed with `gzip` (use the `z` option in `tar`).

## Examples

After creating the backup archive, your `backups` directory should contain the new compressed archive:

```bash
~/project/backups/
└── system-backup.tar.gz
```

When you run `ls -lh ~/project/backups/`, you should see the archive file with its size:

```plaintext
-rw-rw-r-- 1 labex labex 1.2K Sep 11 15:08 system-backup.tar.gz
```

## Hints

- The common options for creating a compressed archive with `tar` are `c` (create), `z` (compress with gzip), `v` (verbose, to see progress), and `f` (specify filename).
- The `-T` option tells `tar` to get the names of the files to archive from the file that follows, instead of from the command line.
- The command structure will look something like `tar -czvf [archive_name] -T [list_file]`.

Continue


Inspect Result

Check if the backup archive exists

Check if the archive includes the required backup files

Continue



Well done!  
Now, let's go to the next step.


# Verifying Backup Integrity

A backup is useless if it's corrupted or incomplete. A crucial step in any backup strategy is verification. You must ensure that the archive you created contains all the intended files and is readable.

## Tasks

- Use the `tar` command to list the contents of the `system-backup.tar.gz` archive without extracting it.
- Redirect the output of this command to a new file named `backup-contents.txt` in the `~/project` directory.

## Requirements

- You must create a file named `backup-contents.txt` in `~/project`.
- This file must contain the list of all files and directories stored within `system-backup.tar.gz`.
- Do not extract the files; only list them.

## Examples

After creating the verification file, your project directory should contain the new `backup-contents.txt` file:

```bash
~/project/
├── backup-contents.txt
├── backup-list.txt
├── backups/
├── config/
├── data/
└── logs/
```

When you run `cat backup-contents.txt`, you should see a detailed listing of all files in the archive:

```plaintext
drwxrwxr-x labex/labex       0 2025-09-11 15:08 data/
-rw-rw-r-- labex/labex      46 2025-09-11 15:08 data/transactions.csv
drwxrwxr-x labex/labex       0 2025-09-11 15:08 config/
-rw-rw-r-- labex/labex      72 2025-09-11 15:08 config/app.conf
drwxrwxr-x labex/labex       0 2025-09-11 15:08 logs/
-rw-rw-r-- labex/labex      49 2025-09-11 15:08 logs/app.log
```

## Hints

- The `tar` command has an option to list (`t`) the contents of an archive.
- Combine the `t` option with `z` (for gzip), `v` (for a detailed list), and `f` (to specify the file).
- Use the output redirection operator `>` to save the command's output to a file.

Continue



Inspect Result

Check if backup-contents.txt exists

Check if backup-contents.txt lists the archived files

Continue



# Restoring Files from a Backup

Disaster strikes! A junior developer, while trying to clear some space, has accidentally deleted the main application configuration file, `app.conf`. The application is now down. It's up to you, the Backup Sentinel, to restore this critical file from your backup and save the day.

## Tasks

1. First, simulate the accident by deleting the `config/app.conf` file.
2. Then, use the `tar` command to restore _only_ the `config/app.conf` file from your `system-backup.tar.gz` archive. The file should be restored to its original location.

## Requirements

- The file `~/project/config/app.conf` must be present after you complete the task.
- You must extract only the single `config/app.conf` file, not the entire archive.

## Examples

After restoring the `app.conf` file, your `config` directory should contain the restored file:

```bash
~/project/config/
├── app.conf
└── ...
```

When you run `ls -l ~/project/config/app.conf`, you should see the restored file:

```plaintext
-rw-rw-r-- 1 labex labex 72 Sep 11 15:08 /home/labex/project/config/app.conf
```

You can verify the file content is correct by checking it contains the expected configuration:

```bash
# This should show the database and API key settings
cat ~/project/config/app.conf
```

## Hints

- The `rm` command is used to delete files.
- The `tar` command uses the `x` option to extract files.
- To extract a specific file, you can add its path (as it appears in the archive) to the end of the `tar -x` command.
- The full path to the file inside the archive is `config/app.conf`.

Continue



Inspect Result

Check if app.conf has been restored

Check if the restored file matches the backup

Continue


Good job!  
Let's proceed to the following step.



# Scheduling Automated Backup Tasks

You saved the day, but a hero's work is never done. Relying on manual backups is risky. The final step is to automate the process so that backups are created regularly without any human intervention. For this, we will use `cron`, the standard Linux task scheduler.

## Tasks

- Create a cron job that automatically runs a backup command.
- The job should run **every minute** (for the purpose of this challenge).
- The command should create a new, compressed `tar` archive inside the `~/project/backups/` directory.
- To prevent overwriting, each new backup file must have a unique name that includes a timestamp (e.g., `backup-2023-10-27_15-30-00.tar.gz`).

## Requirements

- You must use `crontab -e` to edit your cron jobs.
- The cron schedule must be `* * * * *` to run every minute.
- The backup command within the cron job must use an **absolute path** for the output directory.
- The backup command must back up the `data`, `config`, and `logs` directories from `/home/labex/project`. You may either list the directories directly after `-C /home/labex/project` or reuse the `/home/labex/project/backup-list.txt` manifest with `-T`.
- The backup filename must include a timestamp.

## Examples

After setting up the cron job, you can verify it's working by checking your crontab and waiting for automatic backups to appear. When you run `crontab -l`, you should see your new backup job:

```bash
# Example output (your exact command may vary)
* * * * * tar -czf /home/labex/project/backups/backup-$(date +\%Y-\%m-\%d_\%H-\%M-\%S).tar.gz -C /home/labex/project data config logs
```

The same backup can also reuse the manifest you created earlier:

```bash
# Example output (your exact command may vary)
* * * * * tar -czf /home/labex/project/backups/backup-$(date +\%Y-\%m-\%d_\%H-\%M-\%S).tar.gz -C /home/labex/project -T /home/labex/project/backup-list.txt
```

After a minute or two, your `backups` directory should start showing timestamped backup files:

```bash
~/project/backups/
├── backup-2025-09-11_15-30-00.tar.gz
├── backup-2025-09-11_15-31-00.tar.gz
├── backup-2025-09-11_15-32-00.tar.gz
└── system-backup.tar.gz
```

## Hints

- Run `crontab -e` to open the cron job editor. You may be asked to select an editor; `nano` is a good choice.
- The format for a cron job is: `[minute] [hour] [day_of_month] [month] [day_of_week] [command]`. `* * * * *` means every minute of every hour of every day.
- You can use the `date` command to generate a timestamp. For example, `date +%Y-%m-%d_%H-%M-%S` will produce a format like `2023-10-27_15-30-00`.
- To use the output of a command within another command, use `$(command)`.
- **Important:** In a crontab, the percent sign (`%`) has a special meaning (it's treated as a newline). You must escape it with a backslash (`\%`) when using it with the `date` command.
- Your final command in the crontab might look like: `* * * * * tar -czf /path/to/backup-$(date +\%F_\%T).tar.gz -C /path/to/source dir1 dir2`