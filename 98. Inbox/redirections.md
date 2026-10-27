# Linux Redirections — Complete Practical Notes

These notes will take you from beginner to advanced Linux redirections, with explanations, terminal examples, practice exercises, and real-world Bash scripting examples. You can use them as your Linux system administration and penetration-testing lab notes.

All examples are designed for Ubuntu or a similar Linux environment. You can practise them inside your Podman container or a Linux lab.

## 1. What is redirection in Linux?

Redirection means changing where a command gets its input from or sends its output to.

Normally, a command takes input from your keyboard and displays output on your terminal. Redirection lets you send that output to a file, discard it, or combine it with other output.

For example:

```
ls
```

Displays the directory contents in your terminal.

```
ls > files.txt
```

Saves the directory contents to `files.txt` instead.

```
ls > files.txt 2> errors.txt
```

Saves normal output to `files.txt` and error messages to `errors.txt`.

Redirection is especially useful for:

- Saving command output in log files.
    
- Recording errors for troubleshooting.
    
- Creating and updating files from scripts.
    
- Building backups and automation scripts.
    
- Monitoring services and system processes.
    
- Combining commands into pipelines.
    
- Capturing output during security testing in an authorized lab.
    

## 2. Understand the three standard streams

Linux programs normally use three standard data streams.

## 0

stdin

Standard input — `stdin`

The data a program reads. Usually, this is your keyboard.

Example: `read name` reads input from the user.

## 1

stdout

Standard output — `stdout`

Normal output produced by a command.

Example: `echo "Hello"` prints `Hello`.

## 2

stderr

Standard error — `stderr`

Diagnostic messages and errors.

Example: `ls /missing_directory` normally writes an error here.

The numbers `0`, `1`, and `2` are called file descriptors (FDs). They identify these standard streams.

A useful mental model:

```
Keyboard ──> stdin (0) ──> Command
                           │
                 ┌─────────┴─────────┐
                 ▼                   ▼
             stdout (1)          stderr (2)
             Normal output       Error output
```

The streams can be redirected independently.

## 3. The most important redirection operators

|Operator|Meaning|
|---|---|
|`>`|Write output to a file, replacing its existing contents|
|`>>`|Append output to a file|
|`<`|Read input from a file|
|`2>`|Redirect standard error|
|`2>>`|Append standard error|
|`2>&1`|Send standard error to the current destination of standard output|
|`&>`|Redirect both standard output and standard error in Bash|
|`&>>`|Append both streams in Bash|
|`<<`|Start a here-document|
|`<<<`|Supply a string as input using a here-string|
|`\\|`|Pipe standard output into another command|
|`\\|&`|Pipe standard output and standard error in Bash|

We will examine each operator with practical examples.

## 4. Output redirection using `>`

The `>` operator redirects standard output to a file.

### Example 1: Save command output

```
mkdir -p ~/redirection-lab
cd ~/redirection-lab

ls -l > files.txt
cat files.txt
```

Explanation:

- `mkdir -p` creates the practice directory if it does not exist.
    
- `cd` moves into the directory.
    
- `ls -l` produces a detailed directory listing.
    
- `>` sends that output into `files.txt`.
    
- `cat files.txt` displays the saved contents.
    

### Example 2: Write text to a file

```
echo "Linux redirection is useful" > notes.txt
cat notes.txt
```

Output:

```
Linux redirection is useful
```

### Example 3: Overwrite an existing file

```
echo "First line" > demo.txt
echo "Second line" > demo.txt

cat demo.txt
```

Output:

```
Second line
```

Why? The second `>` truncates the file before writing the new output. The first line is lost.

Important: `>` does not mean "write only if the file is empty." It replaces the existing file contents.

### Example 4: Save output from multiple commands

```
date > system_report.txt
whoami >> system_report.txt
uname -a >> system_report.txt

cat system_report.txt
```

Here, the first command creates or replaces the report, while the next two append their output. We will explain `>>` next.

## 5. Append output using `>>`

The `>>` operator adds output to the end of a file without deleting existing contents.

```
echo "First line" > demo.txt
echo "Second line" >> demo.txt
echo "Third line" >> demo.txt

cat demo.txt
```

Output:

```
First line
Second line
Third line
```

Compare the operators:

|Command|Result|
|---|---|
|`echo "A" > file.txt`|Creates or replaces the file contents|
|`echo "B" >> file.txt`|Adds `B` to the end|
|`date >> log.txt`|Appends the current date and time|
|`whoami >> log.txt`|Appends the current username|

### Real-world example: create a log

```
echo "Backup started: $(date)" >> backup.log
echo "Backup finished: $(date)" >> backup.log

cat backup.log
```

`$(date)` is command substitution: the shell runs `date` and inserts its output into the text.

### Check the difference

```
printf 'one\ntwo\n' > example.txt
printf 'three\n' >> example.txt

cat example.txt
wc -l example.txt
```

The file will contain three lines, and `wc -l` will report `3`.

## 6. Redirect errors using `2>`

Remember:

- `1` is standard output.
    
- `2` is standard error.
    

The command below attempts to list a nonexistent directory:

```
ls /this_directory_does_not_exist
```

It displays an error on the terminal.

Now redirect the error:

```
ls /this_directory_does_not_exist 2> errors.txt
```

The error message is saved in `errors.txt` instead of being displayed on the terminal.

```
cat errors.txt
```

You should see a message similar to:

```
ls: cannot access '/this_directory_does_not_exist': No such file or directory
```

The exact wording may vary by system.

### Why `2>` and not `>`?

Compare:

```
ls /this_directory_does_not_exist > output.txt
```

This redirects standard output only. The error can still appear in your terminal.

```
ls /this_directory_does_not_exist 2> errors.txt
```

This redirects standard error only.

You can even separate normal output and errors from the same command:

```
ls /etc /missing_directory > output.txt 2> errors.txt
```

Now inspect both files:

```
cat output.txt
cat errors.txt
```

The valid directory listing goes into `output.txt`; the missing-directory error goes into `errors.txt`.

### Append errors using `2>>`

```
ls /missing_one 2>> errors.log
ls /missing_two 2>> errors.log
```

Both error messages are appended to `errors.log`.

This is useful when you want to preserve diagnostic history across multiple runs.

## 7. Redirect both standard output and standard error

Sometimes you want everything in a single log file.

### Method 1: `> file 2>&1`

```
ls /etc /missing_directory > combined.log 2>&1
```

This is a very important pattern.

Read it from left to right:

1. `> combined.log` redirects standard output (FD 1) to `combined.log`.
    
2. `2>&1` redirects standard error (FD 2) to wherever standard output is currently going.
    

Both streams now go into the same file.

```
cat combined.log
```

### What does `2>&1` actually mean?

Break it down:

- `2` means standard error.
    
- `>` means redirect.
    
- `&1` means refer to file descriptor 1, rather than a filename literally called `1`.
    

The `&` matters. Without it, `2>1` means "write standard error to a file named `1`."

### Method 2: Bash shortcut `&>`

```
ls /etc /missing_directory &> combined.log
```

In Bash, this redirects both standard output and standard error to the same file, replacing its contents.

To append both streams in Bash:

```
ls /etc /missing_directory &>> combined.log
```

These shortcuts are convenient in Bash scripts. For portable shell scripts, the explicit `> file 2>&1` form is a good habit.

### Order matters!

Compare these two commands:

```
ls /etc /missing_directory > combined.log 2>&1
```

```
ls /etc /missing_directory 2>&1 > combined.log
```

They are not equivalent.

In the first command, stdout is redirected to `combined.log`, then stderr is redirected to the current destination of stdout. Both go into the file.

In the second command, stderr first copies stdout's original destination, usually the terminal. Then stdout is redirected to the file. The normal listing goes into the file, but the error may still appear on the terminal.

This happens because shell redirections are processed from left to right.

## 8. Discard output with `/dev/null`

Linux provides a special device called `/dev/null`. Data written to it is discarded.

Think of it as a waste bin for command output.

### Hide normal output

```
ls /etc > /dev/null
```

The listing is discarded.

### Hide errors

```
ls /missing_directory 2> /dev/null
```

The error message is discarded.

### Hide both

```
ls /etc /missing_directory > /dev/null 2>&1
```

Neither stream is displayed.

### Why use `/dev/null`?

For example, you may want to test whether a directory exists without printing an error:

```
if ls /etc >/dev/null 2>&1; then
    echo "The directory is readable and listing succeeded"
else
    echo "The command failed"
fi
```

However, for a straightforward directory check, prefer:

```
if [ -d /etc ]; then
    echo "Directory exists"
fi
```

The `-d` test checks whether the path is a directory.

Important: hiding output does not make a command succeed. The command's exit status still indicates success or failure.

## 9. Input redirection using `<`

The `<` operator supplies input from a file rather than the keyboard.

First, create a file:

```
printf 'apple\nbanana\norange\n' > fruits.txt
```

Now read it with `cat`:

```
cat < fruits.txt
```

Output:

```
apple
banana
orange
```

For `cat`, this is equivalent in visible result to `cat fruits.txt`, but the shell is doing something different: it opens the file and connects it to standard input.

### Example: count lines

```
wc -l < fruits.txt
```

Output:

```
3
```

Compare:

```
wc -l fruits.txt
```

This usually displays both the count and the filename:

```
3 fruits.txt
```

With `< fruits.txt`, `wc` reads standard input and usually prints just the count.

### Example: search input from a file

```
grep "banana" < fruits.txt
```

Output:

```
banana
```

You can also use `grep "banana" fruits.txt`; input redirection is helpful when a program expects standard input.

## 10. Combine input and output redirection

You can read from one file and write to another.

```
sort < fruits.txt > sorted.txt
cat sorted.txt
```

If `fruits.txt` contains:

```
orange
apple
banana
```

Then `sorted.txt` will contain:

```
apple
banana
orange
```

Another example:

```
grep "ERROR" < application.log > errors_only.log
```

This reads `application.log` through standard input and writes matching lines to `errors_only.log`.

If the output file already exists, `>` replaces its contents.

### Redirection does not create missing parent directories

This command fails if the `reports` directory does not exist:

```
date > reports/today.txt
```

Create the directory first:

```
mkdir -p reports
date > reports/today.txt
```

## 11. Here-documents using `<<`

A here-document provides multiple lines of input directly in a command or script.

Syntax:

```
command <<EOF
first line
second line
third line
EOF
```

The closing delimiter must appear on a line by itself.

### Example 1: Write multiple lines into a file

```
cat > notes.txt <<EOF
Linux Basics
Redirection
Shell Scripting
System Administration
EOF
```

Check the result:

```
cat notes.txt
```

This is especially convenient for generating configuration files, reports, and sample lab data.

### Example 2: Pass multiple lines to a command

```
wc -l <<EOF
apple
banana
orange
grape
EOF
```

Output:

```
4
```

The lines are supplied to `wc` as standard input.

### Example 3: Variable expansion

```
USER_NAME="SatH"

cat <<EOF
Welcome, $USER_NAME
Today is $(date)
EOF
```

The shell expands `$USER_NAME` and `$(date)` before passing the text to `cat`.

### Example 4: Prevent variable expansion

Quote the delimiter:

```
cat <<'EOF'
This is $HOME
This is $(date)
EOF
```

Output:

```
This is $HOME
This is $(date)
```

The quoted delimiter prevents parameter expansion and command substitution in the here-document.

Security tip: use quoted here-document delimiters when you want to write literal shell syntax or configuration content and do not want variables expanded.

## 12. Here-strings using `<<<`

A here-string sends a single string to a command's standard input. In Bash, it appends a newline to the supplied string.

Syntax:

```
command <<< "some text"
```

### Example 1: Count words

```
wc -w <<< "Linux redirection is powerful"
```

Output:

```
4
```

### Example 2: Search a string

```
grep "admin" <<< "user admin guest"
```

Output:

```
user admin guest
```

### Example 3: Read a value in a script

```
read -r name <<< "SatH"
echo "Hello, $name"
```

Output:

```
Hello, SatH
```

### Here-document versus here-string

|Feature|`<<`|`<<<`|
|---|---|---|
|Input|Multiple lines|One string|
|Typical use|Templates and blocks of text|Quick input to a command|
|Example|`cat <<EOF ... EOF`|`grep x <<< "text"`|

Both are Bash features commonly available on Ubuntu.

## 13. Pipes using `|`

A pipe connects the standard output of one command to the standard input of another command.

```
Command A ── stdout ──> Pipe ── stdin ──> Command B
```

For example:

```
ls /etc | grep ssh
```

Here:

1. `ls /etc` lists the contents of `/etc`.
    
2. `|` sends its standard output to the next command.
    
3. `grep ssh` displays lines containing `ssh`.
    

### Example: Count files

```
ls /etc | wc -l
```

This counts the lines produced by `ls`. It is a quick estimate, not necessarily an exact count of directory entries in every situation.

### Example: Search a log

```
grep "ERROR" application.log | wc -l
```

This counts the matching lines containing `ERROR`.

### Example: Sort and remove duplicates

```
printf 'apple\nbanana\napple\norange\nbanana\n' | sort | uniq
```

Output:

```
apple
banana
orange
```

Multiple pipes form a pipeline:

```
cat application.log | grep "ERROR" | sort | uniq
```

This works, but the first `cat` is unnecessary. Prefer:

```
grep "ERROR" application.log | sort | uniq
```

An even simpler way to sort and deduplicate lines is:

```
sort -u application.log
```

### Redirection versus piping

|Operator|Purpose|
|---|---|
|`>`|Save standard output to a file|
|`<`|Read standard input from a file|
|`\\|`|Send one command's standard output to another command|

For example:

```
grep "ERROR" app.log > errors.txt
```

Saves matching lines to a file.

```
grep "ERROR" app.log | wc -l
```

Counts matching lines by passing them to another command.

## 14. Pipe standard error using `|&`

In Bash, `|&` connects both standard output and standard error to the next command's standard input. It is effectively a shorthand for `2>&1 |`.

Example:

```
ls /etc /missing_directory |& grep "cannot access"
```

This allows the error message to be passed to `grep`, so you can filter it.

Compare:

```
ls /etc /missing_directory | grep "cannot access"
```

The second command normally searches only the standard output stream. Errors go to the terminal rather than through the pipe.

This difference is useful when troubleshooting commands whose diagnostic output must be filtered or processed.

## 15. Use `tee` to display and save output

The `tee` command is especially useful when you want to see output on the terminal and save it to a file at the same time.

```
ls /etc | tee directory_list.txt
```

The listing appears on your screen and is saved to `directory_list.txt`.

### Append instead of overwrite

```
date | tee -a activity.log
```

The `-a` option appends to the file.

### Save output from multiple commands

```
{
    echo "Report generated:"
    date
    echo "Current user:"
    whoami
    echo "Kernel:"
    uname -r
} | tee system_report.txt
```

The braces group the commands so their combined standard output is passed to `tee`.

### Save errors as well

```
ls /etc /missing_directory 2>&1 | tee command.log
```

This displays and records both normal output and error messages.

Important: `tee` normally saves standard input, not standard error automatically. The `2>&1` connects standard error to the stream that `tee` receives.

### Write to a protected file with `sudo`

This is a common Linux administration trap:

```
sudo echo "Example" > /etc/example.conf
```

This may fail with a permission error because your shell opens `/etc/example.conf` before running `sudo echo`.

A better approach is:

```
echo "Example" | sudo tee /etc/example.conf
```

This lets `tee` open the file with elevated privileges.

For appending:

```
echo "Another line" | sudo tee -a /etc/example.conf
```

Be careful: writing to a real system configuration file can change system behaviour. Practise first with a test file.

## 16. File descriptors and advanced redirection

A file descriptor is a small integer used by a process to refer to an open input/output resource.

The standard descriptors are:

```
0 = stdin
1 = stdout
2 = stderr
```

Shells can also use custom descriptors, such as `3` or `4`, to manage additional streams.

### Example 1: Redirect a custom descriptor

```
exec 3> custom.log
echo "This goes to the terminal"
echo "This goes to the log" >&3
exec 3>&-
```

Explanation:

- `exec 3> custom.log` opens `custom.log` for writing and connects FD 3 to it.
    
- `echo "This goes to the log" >&3` sends that text to FD 3.
    
- `exec 3>&-` closes FD 3.
    

Check the file:

```
cat custom.log
```

Output:

```
This goes to the log
```

### Example 2: Close standard output

```
echo "You can see this"
echo "This is discarded" >&-
```

The second command's attempt to write to closed standard output will normally produce a shell diagnostic on standard error and a nonzero exit status. Closing a descriptor is different from redirecting it to `/dev/null`.

To discard output, prefer:

```
echo "This is discarded" > /dev/null
```

### Example 3: Duplicate standard output

```
echo "Hello" >&1
```

This explicitly sends output to standard output, so `Hello` appears on the terminal.

Likewise:

```
echo "Warning" >&2
```

This writes `Warning` to standard error.

The text may look identical on your screen, but the streams can be separated when the command is redirected.

## 17. Redirect standard input and output inside a Bash script

Redirection is especially powerful when you combine it with variables, conditionals, loops, and exit statuses.

Create a script:

```
nano log_report.sh
```

Add this code:

```
#!/usr/bin/env bash

LOG_FILE="$HOME/redirection-lab/report.log"
ERROR_FILE="$HOME/redirection-lab/errors.log"

mkdir -p "$HOME/redirection-lab"

echo "Report started: $(date)" >> "$LOG_FILE"

if ls /etc >/dev/null 2>&1; then
    echo "Successfully listed /etc" >> "$LOG_FILE"
else
    echo "Could not list /etc" >> "$ERROR_FILE"
fi

echo "Report completed: $(date)" >> "$LOG_FILE"
```

Save the file, then run:

```
chmod +x log_report.sh
./log_report.sh
```

Inspect the logs:

```
cat "$HOME/redirection-lab/report.log"
cat "$HOME/redirection-lab/errors.log"
```

The second command may report that the file does not exist if no error was recorded. That is expected.

Why this is useful:

- `>>` preserves previous report entries.
    
- `>/dev/null 2>&1` suppresses both streams from the test command.
    
- `if` evaluates the command's exit status.
    
- Variables make the paths reusable.
    
- Quoting paths protects against problems with spaces and glob characters.
    

One improvement for a real monitoring script is to record errors with timestamps and check the actual condition you care about rather than relying on a generic command's success.

## 18. Redirection in backup scripts

Here is a practical example that creates a compressed backup of a directory and records output and errors separately.

Create a sample project:

```
mkdir -p ~/redirection-lab/project
mkdir -p ~/redirection-lab/backups
mkdir -p ~/redirection-lab/logs

echo "Application configuration" \
    > ~/redirection-lab/project/config.txt

echo "Sample application data" \
    > ~/redirection-lab/project/data.txt
```

Create the script:

```
nano ~/redirection-lab/backup.sh
```

Add:

```
#!/usr/bin/env bash

set -u

SOURCE="$HOME/redirection-lab/project"
BACKUP_DIR="$HOME/redirection-lab/backups"
LOG_FILE="$HOME/redirection-lab/logs/backup.log"
ERROR_FILE="$HOME/redirection-lab/logs/backup-errors.log"

mkdir -p "$BACKUP_DIR" "$(dirname "$LOG_FILE")" ||
    exit 1

BACKUP_FILE="$BACKUP_DIR/project-$(date +%Y%m%d-%H%M%S).tar.gz"

if [[ ! -d "$SOURCE" ]]; then
    echo "ERROR: Source directory does not exist: $SOURCE" \
        >> "$ERROR_FILE"
    exit 1
fi

echo "Backup started: $(date)" >> "$LOG_FILE"

if tar -czf "$BACKUP_FILE" -C "$(dirname "$SOURCE")" \
    "$(basename "$SOURCE")" 2>> "$ERROR_FILE"; then

    echo "Backup successful: $BACKUP_FILE" >> "$LOG_FILE"
    echo "Backup completed: $(date)" >> "$LOG_FILE"

else
    echo "ERROR: Backup failed: $(date)" >> "$ERROR_FILE"
    exit 1
fi
```

Run it:

```
chmod +x ~/redirection-lab/backup.sh
~/redirection-lab/backup.sh
```

Inspect the results:

```
ls -lh ~/redirection-lab/backups
cat ~/redirection-lab/logs/backup.log
cat ~/redirection-lab/logs/backup-errors.log
```

The error file may not exist if no errors have been recorded.

Verify the archive:

```
tar -tzf ~/redirection-lab/backups/*.tar.gz
```

This lists archive contents without extracting them.

Notice how `2>> "$ERROR_FILE"` captures errors from `tar` while the script explicitly logs success or failure. The `if` condition matters: simply redirecting output does not establish whether a backup succeeded.

This is a useful foundation for later adding retention policies, integrity checks, `systemd` timers, and `cron`.

## 19. Advanced redirection: process substitution

Bash process substitution looks like this:

```
<(command)
>(command)
```

It lets a command use the output or input of another command as if it were a file-like path.

### Example 1: Compare two command outputs

```
diff <(sort file1.txt) <(sort file2.txt)
```

This compares the sorted contents of two files without needing to create separate sorted temporary files.

Try it:

```
printf 'banana\napple\n' > file1.txt
printf 'apple\nbanana\n' > file2.txt

diff <(sort file1.txt) <(sort file2.txt)
```

No output means the sorted contents match.

### Example 2: Compare two directory listings

```
diff <(ls dir1) <(ls dir2)
```

This compares the listings, not the contents of every file. For more comprehensive directory comparisons, use `diff -r dir1 dir2`.

Process substitution is supported by Bash and some other shells, but it is not POSIX shell syntax.

## 20. Prevent accidental overwriting with `noclobber`

The shell option `noclobber` can help protect existing files from accidental truncation by `>`.

Enable it in the current shell:

```
set -o noclobber
```

Create a file:

```
echo "Important data" > important.txt
```

Try overwriting it:

```
echo "New data" > important.txt
```

The shell should refuse to overwrite the existing file.

You can still append:

```
echo "Additional data" >> important.txt
```

If you intentionally need to overwrite a file, Bash supports:

```
echo "Replace data" >| important.txt
```

Disable the protection when appropriate:

```
set +o noclobber
```

This setting is useful in interactive work, but it is not a substitute for backups or careful file management.

## 21. Common mistakes and how to fix them

|Mistake|What happens|Correct approach|
|---|---|---|
|`command > file 2> file`|Both streams open the same file separately; data can be lost or interleaved unexpectedly|`command > file 2>&1`|
|`command 2>1`|Errors go to a file named `1`|Use `2>&1` to redirect errors to stdout's current destination|
|`sudo echo test > /etc/file`|The shell may fail to open the protected file|`echo test \\| sudo tee /etc/file`|
|`command > /missing/dir/file`|Redirection fails because the parent directory is missing|Create the parent directory first|
|`command > file`|Existing contents are truncated|Use `>>` if you need to append|
|`command 2>&1 > file`|Errors may remain on the terminal|Use `command > file 2>&1`|
|`command 2> /dev/null`|Errors disappear, but the command can still fail|Check `$?` or use `if command ...`|
|`cat file \\| grep word`|Unnecessary extra process for simple file searches|`grep word file`|

### A special case: the same file for both streams

Avoid this:

```
some_command > output.log 2> output.log
```

Both redirections open the same file independently. This can truncate the file twice and produce unexpected results.

Instead:

```
some_command > output.log 2>&1
```

Or, in Bash:

```
some_command &> output.log
```

## 22. Check the command's exit status

Redirection changes where output goes; it does not determine whether the command succeeded.

Linux commands commonly return an exit status:

- `0` usually means success.
    
- A nonzero status usually means failure.
    

Example:

```
ls /etc > output.txt 2> errors.txt
echo "$?"
```

The `$?` variable contains the exit status of the most recently completed foreground command. Here, it contains the status of `ls`, because the following `echo` has not run yet.

Now try:

```
ls /missing_directory > output.txt 2> errors.txt
echo "$?"
```

The status will normally be nonzero.

You can use this directly in scripts:

```
if tar -czf backup.tar.gz project/ 2> backup-errors.log; then
    echo "Backup successful"
else
    echo "Backup failed"
fi
```

The `if` statement checks the status of the `tar` command.

A subtle point: in a pipeline such as `command1 | command2`, the pipeline's default exit status is normally the status of the last command. Bash's `set -o pipefail` makes the pipeline fail if a command in it fails.

```
set -o pipefail
tar -czf - project/ | gzip > project.tar.gz
```

This is an example of a pipeline whose failure status is easier to handle correctly when `pipefail` is enabled.

## 23. Redirection with `crontab` and `systemd`

Redirection is commonly used when scripts run automatically without an interactive terminal.

### Cron example

Edit your personal crontab:

```
crontab -e
```

For example, to run a backup script every day at 2:00 AM:

```
0 2 * * * /home/YOUR_USER/redirection-lab/backup.sh >> /home/YOUR_USER/redirection-lab/logs/cron.log 2>&1
```

Replace `YOUR_USER` with your actual Linux username.

This appends both normal output and errors to `cron.log`.

Make sure the script is executable and all paths are correct. Cron has a limited environment, so using absolute paths is good practice.

### Systemd service example

Create a service unit for a script:

```
[Unit]
Description=Redirection Lab Backup

[Service]
Type=oneshot
ExecStart=/home/YOUR_USER/redirection-lab/backup.sh
StandardOutput=append:/home/YOUR_USER/redirection-lab/logs/systemd.log
StandardError=append:/home/YOUR_USER/redirection-lab/logs/systemd-errors.log
```

Save it as a service file under `/etc/systemd/system/`, replacing `YOUR_USER` with your username. This example uses systemd's `append:` output setting; availability depends on the systemd version.

After installing the service:

```
sudo systemctl daemon-reload
sudo systemctl start redirection-lab-backup.service
sudo systemctl status redirection-lab-backup.service
```

You can also inspect service logs with:

```
journalctl -u redirection-lab-backup.service
```

For an actual backup service, choose appropriate file permissions and ensure the service runs as the intended user.

## 24. Practical cheat sheet

Keep this section handy when writing shell scripts.

|Goal|Command|
|---|---|
|Save normal output|`command > output.txt`|
|Append normal output|`command >> output.txt`|
|Save errors|`command 2> errors.txt`|
|Append errors|`command 2>> errors.txt`|
|Save both streams|`command > all.log 2>&1`|
|Append both streams in Bash|`command &>> all.log`|
|Discard normal output|`command > /dev/null`|
|Discard errors|`command 2> /dev/null`|
|Discard both streams|`command > /dev/null 2>&1`|
|Read from a file|`command < input.txt`|
|Read and save output|`command < input.txt > output.txt`|
|Send output to another command|`command1 \\| command2`|
|Display and save output|`command \\| tee output.txt`|
|Append and display output|`command \\| tee -a output.txt`|
|Supply a string as input|`command <<< "text"`|
|Supply multiple lines|`command <<EOF ... EOF`|
|Send stdout to stderr|`echo "Warning" >&2`|
|Send stderr to stdout|`2>&1`|
|Close a custom descriptor|`exec 3>&-`|

## 25. Exercises: beginner to advanced

Use a fresh practice directory so you don't accidentally overwrite important files.

```
mkdir -p ~/redirection-lab/exercises
cd ~/redirection-lab/exercises
```

## Practice checklist

0/11 completed

1. Save command output (Beginner)

Save the output of `pwd` to a file named `current.txt`.

Hint: Use `>`.

2. Append output (Beginner)

Append the output of `date` to `current.txt` without deleting its previous contents.

Hint: Use `>>`.

3. Capture an error (Beginner)

Run `ls /missing_path` and save its error message to `errors.txt`.

Hint: Use `2>`.

4. Read a file (Beginner)

Use `wc -l` with input redirection to count lines in `current.txt`.

Hint: Use `<`.

5. Separate output and errors (Intermediate)

Run `ls /etc /missing_path`, saving normal output to `valid.txt` and errors to `errors.txt`.

Hint: Use both `>` and `2>`.

6. Combine streams (Intermediate)

Save normal output and errors from `ls /etc /missing_path` into `combined.log`.

Hint: Use `> combined.log 2>&1`.

7. Filter a log (Intermediate)

Create a sample log with `INFO` and `ERROR` lines, then save only `ERROR` lines to `error-only.log`.

Hint: Use `grep` and `>`.

8. Display and save (Intermediate)

Display the result of `uname -a` while also saving it to `system.txt`.

Hint: Use `tee`.

9. Build a report (Advanced)

Use a grouped block to record the current date, username, and kernel version in `report.txt`.

Hint: Use `{ ...; } > report.txt`.

10. Create a reusable log script (Advanced)

Write a script that appends a start timestamp, runs a command, captures errors separately, and logs success or failure.

Hint: Use `>>`, `2>>`, variables, and an `if` statement.

11. Automate a backup (Advanced)

Create a script that archives a sample directory, writes a success message only when `tar` succeeds, and appends errors to a separate log.

Hint: Use `tar -czf`, `if`, `2>>`, and `>>`.

Practise remaining exercises with me

## 26. Final rules to remember

The most important things to understand are:

1. `>` replaces file contents, while `>>` appends.
    
2. `2>` redirects errors, while `>` redirects standard output.
    
3. `2>&1` duplicates standard output's current destination for standard error. Order matters.
    
4. `<` supplies input from a file.
    
5. `|` sends one command's output to another command.
    
6. `tee` lets you display and save output at the same time.
    
7. `/dev/null` discards data; it does not guarantee success.
    
8. `<<` and `<<<` supply input directly from your shell script or command line.
    
9. Always check exit statuses when backups and automation depend on a command succeeding.
    
10. Use absolute paths and intentional logging when scripts run through cron or systemd.
    

My recommendation: practise `>`, `>>`, `2>`, `2>&1`, `<`, `|`, and `tee` until you can explain each without looking at the notes. Those seven concepts form the foundation for writing reliable Linux automation and backup scripts.