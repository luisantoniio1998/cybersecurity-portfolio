# Update a File Through a Python Algorithm

## Project Description

As a security professional at a healthcare company, I am responsible for managing access to restricted content. The organization uses an allow list of IP addresses that are permitted to access restricted content. I automated the process of updating this allow list by developing a Python algorithm that:
1. Opens and reads the current allow list file
2. Compares it against a removal list of IP addresses that should no longer have access
3. Removes unauthorized IP addresses
4. Updates the file with the revised allow list

This automation improves efficiency, reduces manual errors, and ensures that access controls are maintained accurately.

---

## Open the File That Contains the Allow List

### Task:
Open the "allow_list.txt" file to read the current list of authorized IP addresses.

### Code:

```python
# Assign `import_file` to the name of the file
import_file = "allow_list.txt"

# Open the file using a with statement
with open(import_file, "r") as file:
```

### Explanation:

The first step is to open the file containing the allow list:

- **`import_file = "allow_list.txt"`**: Assigns the filename to a variable for easy reference and potential reusability
- **`with open(import_file, "r") as file:`**: Opens the file using a `with` statement
  - **`with` statement**: Provides proper resource management, automatically closing the file after the indented block completes (even if an error occurs)
  - **`open()`**: Built-in Python function to open files
  - **`"r"`**: Opens the file in read mode (other modes: "w" for write, "a" for append)
  - **`as file`**: Creates a file object named `file` that can be used to interact with the file contents

Using the `with` statement is a Python best practice because it ensures the file is properly closed, preventing resource leaks and potential file corruption.

---

## Read the File Contents

### Task:
Read the contents of the allow list file and store it in a variable for processing.

### Code:

```python
# Assign `import_file` to the name of the file
import_file = "allow_list.txt"

# Open and read the file
with open(import_file, "r") as file:
    # Read the file contents and store in ip_addresses variable
    ip_addresses = file.read()

# Display the contents
print(ip_addresses)
```

### Output:

```
192.168.25.60
192.168.205.12
192.168.97.225
192.168.6.9
192.168.52.90
192.168.158.170
192.168.90.124
192.168.186.176
192.168.133.188
192.168.203.198
```

### Explanation:

- **`ip_addresses = file.read()`**: Uses the `.read()` method to read the entire contents of the file
  - Returns the contents as a single string
  - Each IP address is separated by a newline character (`\n`)
  - Stores the result in the `ip_addresses` variable

- **`print(ip_addresses)`**: Displays the file contents for verification (optional but useful for debugging)

At this point, the allow list is stored in memory as a string, ready for further processing.

---

## Convert the String into a List

### Task:
Convert the string of IP addresses into a list so we can iterate through and manipulate individual IP addresses.

### Code:

```python
# Assign `import_file` to the name of the file
import_file = "allow_list.txt"

# Assign `remove_list` to a list of IP addresses to be removed
remove_list = ["192.168.97.225", "192.168.158.170", "192.168.201.40", "192.168.58.57"]

# Open and read the file
with open(import_file, "r") as file:
    ip_addresses = file.read()

# Convert the string into a list
ip_addresses = ip_addresses.split()

# Display the list
print(ip_addresses)
```

### Output:

```
['192.168.25.60', '192.168.205.12', '192.168.97.225', '192.168.6.9', '192.168.52.90', '192.168.158.170', '192.168.90.124', '192.168.186.176', '192.168.133.188', '192.168.203.198']
```

### Explanation:

- **`remove_list`**: A list of IP addresses that need to be removed from the allow list (these IPs no longer require access)

- **`ip_addresses.split()`**: Converts the string into a list
  - The `.split()` method splits a string into a list based on whitespace (spaces, tabs, newlines) by default
  - Each IP address becomes a separate element in the list
  - Returns a new list object

Converting to a list is essential because:
- Lists are mutable (can be modified)
- We can iterate through individual IP addresses
- We can use list methods like `.remove()` to delete specific elements

---

## Iterate Through the Remove List

### Task:
Loop through each IP address in the remove list to check if it exists in the allow list.

### Code:

```python
# Assign `import_file` to the name of the file
import_file = "allow_list.txt"

# Assign `remove_list` to a list of IP addresses to be removed
remove_list = ["192.168.97.225", "192.168.158.170", "192.168.201.40", "192.168.58.57"]

# Open and read the file
with open(import_file, "r") as file:
    ip_addresses = file.read()

# Convert string to list
ip_addresses = ip_addresses.split()

# Iterate through remove_list
for element in remove_list:
    print(element)
```

### Output:

```
192.168.97.225
192.168.158.170
192.168.201.40
192.168.58.57
```

### Explanation:

- **`for element in remove_list:`**: Begins a for loop that iterates through each item in `remove_list`
  - **`element`**: Loop variable that takes the value of each IP address in the list, one at a time
  - For each iteration, `element` contains one IP address from the remove list

- **`print(element)`**: Displays each IP address (used here for demonstration; will be replaced with removal logic)

This loop structure allows us to process each IP address that needs to be removed from the allow list.

---

## Remove IP Addresses That Are on the Remove List

### Task:
Check if each IP address in the remove list exists in the allow list, and if so, remove it.

### Code:

```python
# Assign `import_file` to the name of the file
import_file = "allow_list.txt"

# Assign `remove_list` to a list of IP addresses to be removed
remove_list = ["192.168.97.225", "192.168.158.170", "192.168.201.40", "192.168.58.57"]

# Open and read the file
with open(import_file, "r") as file:
    ip_addresses = file.read()

# Convert string to list
ip_addresses = ip_addresses.split()

# Iterate through remove_list and remove IPs from ip_addresses
for element in remove_list:
    if element in ip_addresses:
        ip_addresses.remove(element)

# Display updated list
print(ip_addresses)
```

### Output:

```
['192.168.25.60', '192.168.205.12', '192.168.6.9', '192.168.52.90', '192.168.90.124', '192.168.186.176', '192.168.133.188', '192.168.203.198']
```

### Explanation:

- **`if element in ip_addresses:`**: Conditional statement that checks if the IP address exists in the allow list
  - **`in`**: Membership operator that returns `True` if `element` is found in `ip_addresses`
  - Only proceeds with removal if the IP exists (prevents errors from trying to remove non-existent items)

- **`ip_addresses.remove(element)`**: Removes the first occurrence of `element` from the list
  - **`.remove()`**: List method that deletes the specified value
  - Modifies the list in-place (changes the original list)

In this iteration:
- "192.168.97.225" was found and removed
- "192.168.158.170" was found and removed
- "192.168.201.40" was not in the allow list (no action taken)
- "192.168.58.57" was not in the allow list (no action taken)

The allow list now contains only authorized IP addresses.

---

## Update the File with the Revised List of IP Addresses

### Task:
Convert the updated list back to a string and write it to the file, replacing the old content.

### Code:

```python
# Assign `import_file` to the name of the file
import_file = "allow_list.txt"

# Assign `remove_list` to a list of IP addresses to be removed
remove_list = ["192.168.97.225", "192.168.158.170", "192.168.201.40", "192.168.58.57"]

# Open and read the file
with open(import_file, "r") as file:
    ip_addresses = file.read()

# Convert string to list
ip_addresses = ip_addresses.split()

# Remove IP addresses in the remove_list
for element in remove_list:
    if element in ip_addresses:
        ip_addresses.remove(element)

# Convert the list back to a string
ip_addresses = "\n".join(ip_addresses)

# Write the updated string back to the file
with open(import_file, "w") as file:
    file.write(ip_addresses)

print("Allow list has been updated successfully!")
```

### Explanation:

#### Step 1: Convert List Back to String

```python
ip_addresses = "\n".join(ip_addresses)
```

- **`.join()`**: String method that combines list elements into a single string
- **`"\n"`**: The separator used between elements (newline character)
- **Result**: Each IP address on its own line, formatted exactly as the original file

Example transformation:
```
['192.168.25.60', '192.168.205.12']
→
"192.168.25.60\n192.168.205.12"
```

#### Step 2: Write to File

```python
with open(import_file, "w") as file:
    file.write(ip_addresses)
```

- **`open(import_file, "w")`**: Opens the file in write mode
  - **`"w"`**: Write mode - overwrites the existing file content
  - **Important**: Write mode replaces all existing content
  - The file is created if it doesn't exist

- **`file.write(ip_addresses)`**: Writes the updated string to the file
  - **`.write()`**: File method that writes a string to the file
  - Replaces the old allow list with the updated version

The updated "allow_list.txt" file now contains:
```
192.168.25.60
192.168.205.12
192.168.6.9
192.168.52.90
192.168.90.124
192.168.186.176
192.168.133.188
192.168.203.198
```

---

## Complete Algorithm

### Full Python Script:

```python
# Define a function to update the allow list
def update_file(import_file, remove_list):
    """
    Updates the allow list file by removing specified IP addresses.

    Parameters:
    import_file (str): The name of the file containing the allow list
    remove_list (list): List of IP addresses to be removed

    Returns:
    None
    """

    # Open and read the current allow list
    with open(import_file, "r") as file:
        ip_addresses = file.read()

    # Convert the string into a list
    ip_addresses = ip_addresses.split()

    # Iterate through the remove list
    for element in remove_list:
        # Check if the IP address is in the allow list
        if element in ip_addresses:
            # Remove the IP address
            ip_addresses.remove(element)

    # Convert the list back to a string
    ip_addresses = "\n".join(ip_addresses)

    # Write the updated allow list to the file
    with open(import_file, "w") as file:
        file.write(ip_addresses)

    print(f"Successfully updated {import_file}")
    print(f"Removed {len(remove_list)} IP address(es) from the allow list")


# Main execution
if __name__ == "__main__":
    # Assign the filename
    import_file = "allow_list.txt"

    # Define the list of IP addresses to remove
    remove_list = ["192.168.97.225", "192.168.158.170", "192.168.201.40", "192.168.58.57"]

    # Call the function to update the file
    update_file(import_file, remove_list)

    # Display the updated allow list
    print("\nUpdated allow list:")
    with open(import_file, "r") as file:
        print(file.read())
```

### Output:

```
Successfully updated allow_list.txt
Removed 4 IP address(es) from the allow list

Updated allow list:
192.168.25.60
192.168.205.12
192.168.6.9
192.168.52.90
192.168.90.124
192.168.186.176
192.168.133.188
192.168.203.198
```

---

## Summary

This project demonstrates my ability to automate cybersecurity tasks using Python. I successfully created an algorithm to update an IP address allow list by:

1. **Opening and reading a file** using the `with` statement and `.read()` method
2. **Converting string data to a list** using `.split()` for easier manipulation
3. **Iterating through a removal list** using a `for` loop
4. **Conditionally removing elements** using `if` statements and `.remove()` method
5. **Converting the list back to a string** using `.join()` method
6. **Writing updated data to the file** using write mode and `.write()` method
7. **Organizing code into a reusable function** following best practices

### Python Concepts Applied:
- File I/O operations (reading and writing)
- Context managers (`with` statement)
- String methods (`.split()`, `.join()`)
- List methods (`.remove()`)
- Control flow (`for` loops, `if` statements)
- Functions and documentation
- Variables and data types

### Security Benefits:
- **Automation**: Eliminates manual errors in access control management
- **Efficiency**: Quickly processes multiple IP removals
- **Auditability**: Clear, documented process for access revocation
- **Scalability**: Can handle allow lists of any size
- **Maintainability**: Reusable function that can be integrated into larger security systems

### Potential Enhancements:
- Add logging to track when and which IPs were removed
- Implement error handling for file not found or permission errors
- Add validation to ensure IP addresses are in correct format
- Create a timestamp for each update
- Send notifications when updates occur
- Integrate with a database instead of a text file
- Add command-line arguments for flexibility

This algorithm showcases my practical Python programming skills for automating security tasks, demonstrating my ability to write clean, efficient, and well-documented code for cybersecurity applications.

---

**Project Completed By**: [Your Name]
**Date**: [Current Date]
**Organization**: Healthcare Company - Security Team
**Python Version**: 3.x
**Coding Standards**: PEP 8
