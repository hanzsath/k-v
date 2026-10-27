# Add the shebang and initialize the arrays

Now, let's start writing our script by adding the shebang and initializing our arrays.

1. Add the following content to `array-comparison.sh`:

```bash
#!/bin/bash

# Initialize the arrays
a=(3 5 8 10 6)
b=(6 5 4 12)
c=(14 7 5 7)
```

Let's break this down:

- The first line `#!/bin/bash` is called a shebang. It tells the system to use the Bash interpreter to run this script. This line is crucial for any shell script.
- We then initialize three arrays: `a`, `b`, and `c`. In Bash, arrays are defined by enclosing the elements in parentheses `()` and separating them with spaces.
- Each array contains different integer values. We'll use these arrays to find common elements.

# Implement the first comparison loop

Let's implement the first comparison loop to find common elements between arrays `a` and `b`.

Add the following code to your script:

```bash
# Initialize an array to store common elements between a and b
z=()

# Compare arrays a and b
for x in "${a[@]}"; do
  for y in "${b[@]}"; do
    if [ $x = $y ]; then
      z+=($x)
    fi
  done
done

echo "Common elements between a and b: ${z[@]}"
```

Let's explain this code in detail:

- `z=()` initializes an empty array `z` to store the common elements.
- `for x in "${a[@]}"` is a loop that iterates over each element in array `a`. The `"${a[@]}"` syntax expands to all elements of the array.
- We then have a nested loop `for y in "${b[@]}"` that iterates over each element in array `b`.
- `if [ $x = $y ]` checks if the current element from `a` is equal to the current element from `b`.
- If they are equal, we add this element to array `z` using `z+=($x)`.
- Finally, we print the common elements using `echo "Common elements between a and b: ${z[@]}"`. The `${z[@]}` syntax expands to all elements of array `z`

# Implement the second comparison loop

Now, let's implement the second comparison loop to find common elements between array `c` and the previously found common elements in array `z`.

Add the following code to your script:

```bash
# Initialize an array to store common elements among a, b, and c
j=()

# Compare array c with the common elements found in z
for i in "${c[@]}"; do
  for k in "${z[@]}"; do
    if [ $i = $k ]; then
      j+=($i)
    fi
  done
done

echo "Common elements among a, b, and c: ${j[@]}"
```

This code is similar to the previous loop, but with a few key differences:

- We initialize a new empty array `j` to store the final common elements.
- The outer loop `for i in "${c[@]}"` iterates over elements in array `c`.
- The inner loop `for k in "${z[@]}"` iterates over the common elements we found between `a` and `b`, which are stored in array `z`.
- We compare elements from `c` with elements in `z`, and if there's a match, we add it to array `j`.
- Finally, we print the common elements among all three arrays.

