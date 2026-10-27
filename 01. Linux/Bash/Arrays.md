# Array
#bash #linux
## 1. What Is an Array in Bash?

An **array** is a variable that stores multiple values under one name.
Normally, a variable stores one value:
```bash
username="admin"
```

If we want to store several usernames, we could create separate variables:
```bash
user1="alice"
user2="bob"
user3="charlie"
```

However, managing many variables becomes difficult. Instead, we can use an array:
```bash
users=("alice" "bob" "charlie")
```

Now, all three usernames are stored inside one variable called `users`.
Arrays are useful when working with:
- Multiple filenames or directories.
- Server names and IP addresses.
- Usernames and groups.
- Lists of packages to install.
- Backup files and log files.
- Data that needs to be processed using loops.

Bash supports two main types of arrays:
1. **Indexed arrays** — use numerical indices.
2. **Associative arrays** — use named keys.

---
## 2. Creating an Indexed Array

Indexed arrays are the most common type of array in Bash.
Create a file named `arrays.sh`:
```bash
#!/usr/bin/env bash
servers=("web01" "db01" "backup01")
```

Let's break it down:

- `#!/usr/bin/env bash` selects Bash as the script interpreter.
- `servers` is the array name.
- `=` assigns values to the array.
- `()` contains the array elements.
- Each quoted value is a separate element.

Bash array indices start at `0`, not `1`.

|Index|Value|
|---|---|
|0|web01|
|1|db01|
|2|backup01|

The array contains three elements.
**Important:** Bash arrays can store strings, numbers represented as text, and other ordinary values. They are not restricted to storing only one data type.

### Creating an empty array

```bash
servers=()
```

This creates an empty indexed array.
You can add elements later.

---
## 3. Adding Elements to an Array

There are several ways to add elements.

### Method 1: Initialize multiple elements
```bash
packages=("nginx" "curl" "git")
```

### Method 2: Append elements using `+=`
```bash
packages+=("python3")
packages+=("unzip")
```

The `+=` operator appends elements to the array.
The array now contains:
```
nginx
curl
git
python3
unzip
```

### Method 3: Assign an element to a specific index

```
packages[5]="rsync"
```

This creates an element at index `5`.
There is no element at index `4`, so the array has a gap.

**Important:** Array indices do not need to be consecutive. Appending elements normally adds them after the highest existing index.

---
## 4. Accessing Array Elements

To access an individual element, use its index.
```bash
servers=("web01" "db01" "backup01")

echo "${servers[0]}"
echo "${servers[1]}"
echo "${servers[2]}"
```

Output:
```
web01
db01
backup01
```

Syntax:
```bash
${array_name[index]}
```

For example:
```bash
echo "${servers[1]}"
```

This prints `db01`, because index `1` represents the second element.

### Store an element in another variable

```bash
primary_server="${servers[0]}"
echo "$primary_server"
```

Output:
```
web01
```

### What happens if an index does not exist?

```bash
echo "${servers[10]}"
```

Normally, this prints an empty string because the index is unset.

---
## 5. Displaying All Array Elements

Use `[@]` or `[*]` to expand an array.
```bash
services=("ssh" "nginx" "postgresql")
echo "${services[@]}"
```

Output:
```
ssh nginx postgresql
```

### Difference between `[@]` and `[*]`

When both are quoted:
```bash
printf '<%s>\n' "${services[@]}"
```

Output:
```
<ssh>
<nginx>
<postgresql>
```

Each array element becomes a separate argument.

Now try:
```bash
printf '<%s>\n' "${services[*]}"
```

Output:
```
<ssh nginx postgresql>
```

Inside double quotes, `"${services[*]}"` expands into one combined string, whereas `"${services[@]}"` preserves each element as a separate argument.

**Best practice:** Use `"${array[@]}"` when passing array elements to commands or processing them individually.

---
## 6. Counting Array Elements

Use `${#array[@]}` to count the number of elements.
```bash
files=("report.log" "system.log" "backup.tar")
file_count=${#files[@]}
echo "Total files: $file_count"
```

Output:
```
Total files: 3
```

Explanation:

- `files` is the array name.
- `[@]` refers to all array elements.
- `#` requests the number of elements.

### Count a specific element's characters

Do not confuse these two expressions:
```bash
echo "${#files[@]}"
echo "${#files[0]}"
```

The first prints the number of array elements: `3`.
The second prints the length of the first element: `10`, because `report.log` has ten characters.

---
## 7. Finding the Array's Indices

Use `${!array[@]}` to display the indices that currently exist.
```bash
ports=(
    [22]="ssh"
    [80]="http"
    [443]="https"
)

echo "${!ports[@]}"
```

Output:
```
22 80 443
```

Notice that the indices are not consecutive.
To display the existing indices and their values:
```bash
for port in "${!ports[@]}"; do
    echo "Port $port: ${ports[$port]}"
done
```

Output:
```
Port 22: ssh
Port 80: http
Port 443: https
```

This is particularly useful for arrays with gaps or custom indices.

---
## 8. Looping Through an Array

Loops allow you to process every element without manually accessing each index.

### Method 1: A `for` loop over values
```bash
tools=("nmap" "curl" "git" "tcpdump")

for tool in "${tools[@]}"; do
    echo "Tool: $tool"
done
```

Output:
```
Tool: nmap
Tool: curl
Tool: git
Tool: tcpdump
```

Explanation:

- `for` starts the loop.
- `tool` is a temporary variable that receives one element at a time.
- `"${tools[@]}"` supplies every array element.
- `do` starts the loop body.
- `done` ends the loop.

### Method 2: Loop using indices
```bash
tools=("nmap" "curl" "git")

for index in "${!tools[@]}"; do
    echo "Index: $index, Value: ${tools[$index]}"
done
```

Output:
```
Index: 0, Value: nmap
Index: 1, Value: curl
Index: 2, Value: git
```

Use the first method when you only need the values. Use the second when you also need each element's index.

### Method 3: Process each element using a `while` loop
```bash
tools=("nmap" "curl" "git")

index=0

while (( index < ${#tools[@]} )); do
    echo "${tools[index]}"
    ((index += 1))
done
```

This works for a normal, consecutively indexed array. It does not reliably traverse sparse arrays by position because an array's element count is not necessarily its highest index plus one.

---
## 9. Updating Array Elements

You can replace an existing element by assigning a new value to its index.
```bash
services=("nginx" "apache" "caddy")
services[1]="lighttpd"
printf '%s\n' "${services[@]}"
```

Output:
```
nginx
lighttpd
caddy
```

The value at index `1` has been replaced.
You can also update an element inside a loop:

```bash
for index in "${!services[@]}"; do
    services[$index]="${services[$index]}-service"
done

printf '%s\n' "${services[@]}"
```

Output:
```
nginx-service
lighttpd-service
caddy-service
```

---
## 10. Removing Array Elements

Use `unset` to remove an element.
```bash
logs=("auth.log" "syslog" "kern.log")
unset 'logs[1]'
printf '%s\n' "${logs[@]}"
```

Output:
```
auth.log
kern.log
```

The element at index `1` has been removed. The remaining indices are `0` and `2`.
**Important:** Removing an element does not automatically reindex the array.

### Remove the entire array
```
unset logs
```

This removes the array variable.

### Empty an array without removing the variable
```
logs=()
```

This assigns an empty array.

---
## 11. Searching an Array

Bash does not provide a dedicated built-in array search function, but you can use a loop.
```
users=("alice" "bob" "charlie")

target="bob"
found=false

for user in "${users[@]}"; do
    if [[ "$user" == "$target" ]]; then
        found=true
        break
    fi
done

if [[ "$found" == true ]]; then
    echo "User found: $target"
else
    echo "User not found: $target"
fi
```

Output:
```
User found: bob
```

Explanation:

- `found=false` initializes a flag.
- The loop examines every element.
- `[[ "$user" == "$target" ]]` compares the two strings.
- `found=true` records a match.
- `break` stops the loop once the target is found.

This technique can be used to search package lists, usernames, server names, or filenames.

---
## 12. Getting User Input into an Array

The `read -a` option reads words from a line into an indexed array.
```bash
#!/usr/bin/env bash

read -r -a services -p "Enter service names separated by spaces: "

echo "You entered:"
printf '%s\n' "${services[@]}"

echo "Total services: ${#services[@]}"
```

Example input:
```
ssh nginx docker
```

Output:
```
You entered:
ssh
nginx
docker
Total services: 3
```

This is useful for simple interactive scripts.

**Note:** `read -a` splits input into words according to shell word-splitting rules. It is not a CSV parser and does not automatically preserve arbitrary spaces inside individual entries.

---
## 13. Associative Arrays

An associative array uses string keys instead of numerical indices.
It is useful when each value has a descriptive name.
For example, a server inventory might store a hostname and its IP address.
```bash
#!/usr/bin/env bash

declare -A server_ip

server_ip[web]="192.168.1.10"
server_ip[database]="192.168.1.20"
server_ip[backup]="192.168.1.30"

echo "${server_ip[web]}"
echo "${server_ip[database]}"
```

Output:
```
192.168.1.10
192.168.1.20
```

### Why use `declare -A`?

```
declare -A server_ip
```

This declares `server_ip` as an associative array.
Use Bash 4 or later. Some older systems, notably the default Bash 3.2 on older macOS releases, do not support associative arrays.

### Display all keys and values

```
for server in "${!server_ip[@]}"; do
    echo "$server -> ${server_ip[$server]}"
done
```

This displays the key-value pairs. Their order is not guaranteed.

### Indexed vs associative arrays

|Feature|Indexed array|Associative array|
|---|---|---|
|Declaration|`items=()`|`declare -A items`|
|Key type|Integer index|String key|
|Example|`items[0]="nginx"`|`items[web]="nginx"`|
|Access|`${items[0]}`|`${items[web]}`|
|Typical use|Ordered lists|Named records|

---
## 14. Passing Arrays to Commands Safely

When passing array elements to another command, use `"${array[@]}"`.

Example:
```bash
files=("access log.txt" "error log.txt" "audit.log")

printf 'File: %s\n' "${files[@]}"
```

Output:
```
File: access log.txt
File: error log.txt
File: audit.log
```

Each filename remains a separate argument, even when it contains spaces.
Compare this with an unquoted expansion:

```
printf 'File: %s\n' ${files[@]}
```

The unquoted expansion can split filenames at spaces and expand wildcard characters. Avoid this pattern when processing arbitrary filenames or other data.

### Practical example: Check which commands exist

```bash
#!/usr/bin/env bash

commands=("bash" "grep" "curl" "nmap")

for command_name in "${commands[@]}"; do
    if command -v "$command_name" >/dev/null 2>&1; then
        echo "[OK] $command_name is available"
    else
        echo "[MISSING] $command_name is not installed or not in PATH"
    fi
done
```

Explanation:

- `command -v` checks whether a command is available through the shell.
- `>/dev/null` discards standard output.
- `2>&1` redirects standard error to the same destination as standard output.
- The `if` statement checks the command's exit status.

This is a useful example for Linux administration and lab automation.

---
## 15. Common Bash Array Mistakes

### Mistake 1: Forgetting the braces

Incorrect:
```
echo "$services[0]"
```

Correct:
```
echo "${services[0]}"
```

Without braces, Bash expands `$services` first and treats `[0]` as ordinary text.

### Mistake 2: Using spaces around `=`

Incorrect:
```
services = ("nginx" "ssh")
```

Correct:
```
services=("nginx" "ssh")
```

Bash assignments must not have spaces around the assignment operator.

### Mistake 3: Using the wrong count syntax

Incorrect:
```
echo "${#services}"
```

This does not count the elements of the array.

Correct:
```
echo "${#services[@]}"
```

### Mistake 4: Forgetting to quote array expansions

Risky:
```
command_name ${services[@]}
```

Better:
```
command_name "${services[@]}"
```

Quoting preserves each element as one argument.

### Mistake 5: Assuming array indices are always consecutive

```bash
items=([0]="first" [5]="sixth")

echo "${#items[@]}"
echo "${!items[@]}"
```

Output:
```
2
0 5
```

There are two elements, but the highest index is `5`.

### Mistake 6: Trying to use an indexed array like a dictionary

This declaration is not an associative array:
```
items=()
```

For named keys, use:
```
declare -A items
```

---
## 16. Complete Practical Example: Linux Service Checker

Create `service_checker.sh`:

```bash
#!/usr/bin/env bash

services=("ssh" "cron" "nginx" "docker")

echo "Linux Service Checker"
echo "====================="

echo "Services to check: ${#services[@]}"
echo

for service in "${services[@]}"; do
    if command -v systemctl >/dev/null 2>&1; then
        if systemctl is-active --quiet "$service"; then
            echo "[ACTIVE] $service"
        else
            echo "[NOT ACTIVE] $service"
        fi
    else
        echo "[SKIP] systemctl is unavailable"
        break
    fi
done
```

Run it:

```bash
chmod +x service_checker.sh
./service_checker.sh
```

The script:

1. Stores service names in an array.
2. Counts the elements.
3. Iterates through each service.
4. Checks whether `systemctl` is available.
5. Checks the active state of each service.

**Note:** A service may be missing, inactive, or inaccessible. This example reports non-active states together; a more advanced checker could distinguish those cases. The script also checks service state, not whether the service is enabled to start at boot.

---
## 17. Useful Array Syntax Cheat Sheet

|Syntax|Meaning|
|---|---|
|`items=()`|Create an empty indexed array|
|`items=("a" "b")`|Initialize an array|
|`items+=("c")`|Append an element|
|`${items[0]}`|Access the element at index 0|
|`${items[@]}`|Expand all elements|
|`"${items[@]}"`|Preserve each element as a separate argument|
|`${#items[@]}`|Count array elements|
|`${#items[0]}`|Count characters in the first element|
|`${!items[@]}`|Get existing indices|
|`unset 'items[0]'`|Remove element at index 0|
|`unset items`|Remove the array variable|
|`declare -A items`|Declare an associative array|
|`items[key]="value"`|Assign an associative-array value|
|`[[ -v 'items[0]' ]]`|Test whether an indexed element is set in supported Bash versions|

---

