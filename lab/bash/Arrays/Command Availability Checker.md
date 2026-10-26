Goal: Use an array to check whether important Linux commands are available on your system.

I want to create a small Bash script that checks whether specific Linux commands are installed on my system. While building this script, I also want to learn how to create and use Bash arrays.

---

First, create a folder to store the Bash array exercises.

```bash
mkdir -p bash/array
cd bash/array
vim e01.sh
```

I want the script to display output like this:

**Output:**

```
=== Command Availability Checker ===
[OK] bash
[OK] curl
[OK] git
[OK] tar
[MISSING] nmap

Available: 4
Missing: 2
```

> [!tip] #!/bin/bash
> Start the script with this line to specify Bash as the interpreter.

First, I'll use the `echo` command to display text in the terminal.
Next, I need a list of commands to check. Since I want to practise Bash arrays, I'll store the command names in an array and print them to see how it works.

```bash
#!/bin/bash

echo "=== Command Availability Checking Checker ==="
commands=("nmap" "curl" "wget" "git" "tar")

echo "${commands[@]}"
```

**Output:**

```
=== Command Availability Checking Checker ===
nmap curl wget git tar
```

But printing the entire array on one line isn't quite the output I want. I'll use a `for` loop to go through each command and display it on a separate line.

```bash
#!/bin/bash

echo "=== Command Availability Checking Checker ==="
commands=("nmap" "curl" "wget" "git" "tar")

for command in "${commands[@]}"; do
 echo "[ ] $command"
done
```

Now that I can display each command, I want to check whether it is available on my system. I'll use `command -v` inside an `if` statement to check each command.

```bash
#!/bin/bash

echo "=== Command Availability Checking Checker ==="
commands=("nmap" "curl" "wget" "git" "tar")

for command in "${commands[@]}"; do
 if command -v "$command"; then
  echo "[OK] $command"
 else
  echo "[MISSING] $command"
 fi
done
```

**Output:**

```
=== Command Availability Checking Checker ===
[MISSING] nmap
/usr/bin/curl
[OK] curl
/usr/bin/wget
[OK] wget
[MISSING] git
/usr/bin/tar
[OK] tar
```

When I run the script, I notice that `command -v` also prints the path of each available command. I only want to display my own status messages, so I'll redirect the command's output to `/dev/null`.

```bash
command -v "$command" >/dev/null 2>&1
```

```bash
#!/bin/bash

echo "=== Command Availability Checking Checker ==="
commands=("nmap" "curl" "wget" "git" "tar")

for command in "${commands[@]}"; do
 if command -v "$command" > /dev/null 2>&1; then
  echo "[OK] $command"
 else
  echo "[MISSING] $command"
 fi
done
```

Now the output is cleaner. I'll also change the loop variable from `command` to `cmd` to make the code easier to read and avoid confusion with the `command` builtin.

```bash
#!/bin/bash

echo "=== Command Availability Checking Checker ==="
commands=("nmap" "curl" "wget" "git" "tar")

for cmd in "${commands[@]}"; do
 if command -v "$cmd" > /dev/null 2>&1; then
  echo "[OK] $cmd"
 else
  echo "[MISSING] $cmd"
 fi
done
```

**Output:**

```
=== Command Availability Checking Checker ===
[MISSING] nmap
[OK] curl
[OK] wget
[MISSING] git
[OK] tar
```

![](e01.png)

|Part|Meaning|
|---|---|
|`>`|Redirect standard output (file descriptor 1).|
|`/dev/null`|Discard anything written to it.|
|`2>&1`|Redirect standard error (file descriptor 2) to the current standard output destination.|

In this case, both output streams are discarded, so you see only your own `[OK]` and `[MISSING]` messages.
The order matters: `>/dev/null 2>&1` first redirects standard output and then sends standard error to that destination.

---

The basic checker works, but I want to make it more useful. I'll add counters to track how many commands are available and how many are missing. I'll also display the total number of commands checked.

```bash
#!/bin/bash

echo "=== Command Availability Checking Checker ==="
commands=("nmap" "curl" "wget" "git" "tar")

available=0
missing=0

for cmd in "${commands[@]}"; do
 if command -v "$cmd" > /dev/null 2>&1; then
  echo "[OK] $cmd"
  ((available++))
 else
  echo "[MISSING] $cmd"
  ((missing++))
 fi
done
echo
echo "=== Summary ==="
echo "Available: $available"
echo "Missing:   $missing"
echo "Total:     ${#commands[@]}"
```

**Output:**

```
=== Command Availability Checking Checker ===
[MISSING] nmap
[OK] curl
[OK] wget
[MISSING] git
[OK] tar

=== Summary ===
Available: 3
Missing:   2
Total:     5
```

I want to improve the output further by grouping available commands separately from missing commands.
Instead of displaying each result immediately, I'll create two empty arrays: `available` and `missing`. As the script checks each command, it will add the command to the appropriate array.

```bash
#!/bin/bash

echo "=== Command Availability Checking Checker ==="
commands=("nmap" "curl" "wget" "git" "tar")

available=()
missing=()

for cmd in "${commands[@]}"; do
 if command -v "$cmd" > /dev/null 2>&1; then
  available+=($cmd)
 else
  missing+=($cmd)
 fi
done

for cmd in "${available[@]}"; do
 echo "[OK] $cmd"
done
echo
for cmd in "${missing[@]}"; do
 echo "[MISSING] $cmd"
done

echo
echo "=== Summary ==="
echo "Available: ${#available[@]}"
echo "Missing:   ${#missing[@]}"
echo "Total:     ${#commands[@]}"
```

**Output:**

```
=== Command Availability Checking Checker ===
[OK] curl
[OK] wget
[OK] tar

[MISSING] nmap
[MISSING] git

=== Summary ===
Available: 3
Missing:   2
Total:     5
```

![](e01-v2.png)

Finally, I'll loop through both arrays to display the results separately and show a summary of the total counts.
