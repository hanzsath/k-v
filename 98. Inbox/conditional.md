# Create Your First If Statement

Let's start by creating a simple if statement that checks if a variable called `NAME` is equal to "John".

First, open a terminal in the WebIDE. You should be in the `/home/labex/project` directory by default. If you're not sure, you can always check your current directory with the `pwd` command.

Create a new file called `if.sh` using the following command:

```bash
touch if.sh
```

This command creates an empty file named `if.sh` in your current directory.

Now, open the `if.sh` file in the WebIDE. You can do this by clicking on the file in the file explorer on the left side of the WebIDE.

Add the following content to the file:

```bash
#!/bin/bash

NAME="John"
if [ "$NAME" = "John" ]; then
  echo "The name is John"
fi
```

Let's break down this script:

1. `#!/bin/bash`: This is called a "shebang" line. It tells the system which interpreter to use to run the script. In this case, we're using Bash.
2. `NAME="John"`: This line creates a variable called `NAME` and assigns it the value "John".
3. `if [ "$NAME" = "John" ]; then`: This is the start of our if statement. It checks if the value of `NAME` is equal to "John".
    - The square brackets `[ ]` are actually a command in Bash, equivalent to the `test` command.
    - We put `"$NAME"` in quotes to handle cases where `NAME` might be empty or contain spaces.
    - The semicolon and `then` are part of the if statement syntax in Bash.
4. `echo "The name is John"`: This line will be executed if the condition is true.
5. `fi`: This marks the end of the if statement. It's "if" spelled backwards!

Save the file after adding this content.

Now, we need to make the script executable. In Unix-like systems, files aren't executable by default for security reasons. We can change this using the `chmod` command:

```bash
chmod +x if.sh
```

This command adds the execute permission to the file. The `+x` means "add execute permission".

Now, run the script:

```bash
./if.sh
```

The `./` tells the shell to look for the script in the current directory.

You should see the output: `The name is John`

If you don't see this output, double-check that you've saved the file with the correct content and that you've made it executable.

# Add an Else Clause

Now, let's expand our if statement to include an else clause. This allows us to specify what should happen when the condition is false.

Open the `if.sh` file in the WebIDE again and modify it as follows:

```bash
#!/bin/bash

NAME="Alice"
if [ "$NAME" = "John" ]; then
  echo "The name is John"
else
  echo "The name is not John"
fi
```

Let's go through the changes:

1. We've changed the `NAME` variable to "Alice". This is to demonstrate what happens when the condition is false.
2. We've added an `else` clause. This clause specifies what should happen if the condition in the if statement is false.
3. After the `else`, we've added another `echo` command that will run if `NAME` is not "John".

The `else` clause is optional in if statements, but it's very useful when you want to do something specific when the condition is false, rather than just doing nothing.

Save the file with these changes.

Now, run the script again:

```bash
./if.sh
```

This time, you should see the output: `The name is not John`

This is because `NAME` is now "Alice", so the condition `[ "$NAME" = "John" ]` is false, and the code in the `else` block is executed.

Try changing the `NAME` variable back to "John" and run the script again. What output do you get? This is a good way to test that your if-else statement is working correctly for both cases.


# Numeric Comparisons

Shell scripts can also compare numbers. Let's create a new script to demonstrate numeric comparisons.

Create a new file called `numeric.sh`:

```bash
touch numeric.sh
```

Open `numeric.sh` in the WebIDE and add the following content:

```bash
#!/bin/bash

NUMBER=10

if [ $NUMBER -lt 5 ]; then
  echo "The number is less than 5"
elif [ $NUMBER -eq 10 ]; then
  echo "The number is exactly 10"
elif [ $NUMBER -gt 15 ]; then
  echo "The number is greater than 15"
else
  echo "The number is between 5 and 15, but not 10"
fi
```

This script introduces numeric comparison operators:

- `-lt`: less than
- `-eq`: equal to
- `-gt`: greater than

There are also others you can use:

- `-le`: less than or equal to
- `-ge`: greater than or equal to
- `-ne`: not equal to

Notice that we use these special operators instead of symbols like `<` or `>`. This is because in Bash, `<` and `>` are used for input/output redirection, not for numeric comparison.

Now, let's make the script executable and run it:

```bash
chmod +x numeric.sh
./numeric.sh
```

You should see the output: `The number is exactly 10`

Try changing the `NUMBER` variable to different values and run the script again. See how the output changes based on the value you set.

For example, if you change `NUMBER` to 20, you should get "The number is greater than 15". If you change it to 7, you should get "The number is between 5 and 15, but not 10".

# String Comparisons and Logical Operators

Lastly, let's explore string comparisons and logical operators. Create a new file called `string_logic.sh`:

```bash
touch string_logic.sh
```

Open `string_logic.sh` in the WebIDE and add the following content:

```bash
#!/bin/bash

STRING1="hello"
STRING2="world"
NUMBER1=5
NUMBER2=10

if [ "$STRING1" = "hello" ] && [ "$STRING2" = "world" ]; then
  echo "Both strings match"
fi

if [ $NUMBER1 -lt 10 ] || [ $NUMBER2 -gt 5 ]; then
  echo "At least one of the number conditions is true"
fi

if [[ "$STRING1" != "$STRING2" ]]; then
  echo "The strings are different"
fi

if [[ -z "$STRING3" ]]; then
  echo "STRING3 is empty or not set"
fi
```

This script demonstrates several new concepts:

1. String equality comparison (`=`): This checks if two strings are exactly the same.
2. Logical AND (`&&`): This operator allows you to combine two conditions. Both conditions must be true for the overall expression to be true.
3. Logical OR (`||`): This operator also combines two conditions, but only one needs to be true for the overall expression to be true.
4. String inequality comparison (`!=`): This checks if two strings are different.
5. Checking if a string is empty (`-z`): This is true if the string is empty (has zero length).

Also, notice the use of double square brackets `[[ ]]` in some of the if statements. These are an enhanced version of the single square brackets and are preferred in Bash scripts when possible. They allow for more complex expressions and have fewer surprises with regard to word splitting and pathname expansion.

Make the script executable and run it:

```bash
chmod +x string_logic.sh
./string_logic.sh
```

You should see all four echo statements printed, because all the conditions in the script are true.

```plaintext
Both strings match
At least one of the number conditions is true
The strings are different
STRING3 is empty or not se
```

Try changing some of the values (like setting `STRING1` to something other than "hello") and see how it affects the output.

# Complete the Weather Advisory Script

## Tasks

1. Navigate to the `~/project` directory where you'll find a partially completed script named `weather_advisor.sh`.
2. Open the `weather_advisor.sh` file and complete the if statements to provide weather advice based on the temperature input.

## Requirements

- The script `weather_advisor.sh` is already created in the `~/project` directory with a basic structure.
- The script already includes the shebang and user input prompt.
- Your task is to complete the if statements to provide the following advice:
    - If the temperature is below 0°C: "It's freezing! Wear a heavy coat and gloves."
    - If the temperature is between 0°C and 10°C (inclusive): "It's cold. A warm jacket is recommended."
    - If the temperature is between 11°C and 20°C (inclusive): "It's cool. A light jacket should suffice."
    - If the temperature is above 20°C: "It's warm. Enjoy the pleasant weather!"
- Use `echo` to display the advice to the user.

## Example

Here's an example of how the completed script should work:

```
$ ./weather_advisor.sh
Enter the current temperature in Celsius: 15
It's cool. A light jacket should suffice.

$ ./weather_advisor.sh
Enter the current temperature in Celsius: -2
It's freezing! Wear a heavy coat and gloves.

$ ./weather_advisor.sh
Enter the current temperature in Celsius: 25
It's warm. Enjoy the pleasant weather!
```

The script's strings must reference the examples and remain unchanged to prevent test failures.


```bash
#!/bin/bash

  

echo "Enter the current temperature in Celsius: "

# The 'read' command is used to accept user input

# It reads a line from standard input and assigns it to the variable 'temp'

read temp

  

# TODO: Implement if statements here to provide weather advice based on the temperature

# Use the following temperature ranges:

# Below 0°C: "It's freezing! Wear a heavy coat and gloves."

# 0°C to 10°C: "It's cold. A warm jacket is recommended."

# 11°C to 20°C: "It's cool. A light jacket should suffice."

# Above 20°C: "It's warm. Enjoy the pleasant weather!"

  

if [ $temp -lt 0 ]; then

echo "It's freezing! Wear a heavy coat and gloves."

elif [ $temp -ge 0 ] && [ $temp -le 10 ]; then

echo "It's cold. A warm jacket is recommended."

elif [ $temp -ge 11 ] && [ $temp -le 20 ]; then

echo "It's cool. A light jacket should suffice."

else

echo "It's warm. Enjoy the pleasant weather!"

fi
```