# File Permissions in Linux

## Project Description

As a security professional at a large organization working with the research team, I was tasked with examining and managing file permissions on the Linux file system. This project involved auditing existing permissions to ensure they matched the authorized access levels for the research team. When permissions did not align with authorization requirements, I modified them using Linux commands to ensure appropriate users had the correct access while removing any unauthorized access. This demonstrates my ability to manage file system security and implement the principle of least privilege in a Linux environment.

---

## Check File and Directory Details

To begin the audit, I needed to examine the current permissions for files and subdirectories in the `projects` directory, including hidden files.

### Command Used:

```bash
ls -la /home/researcher2/projects
```

### Output:

```
total 32
drwxr-xr-x 3 researcher2 research_team 4096 Nov 15 10:30 .
drwxr-xr-x 5 researcher2 research_team 4096 Nov 15 09:15 ..
-rw--w---- 1 researcher2 research_team   46 Nov 15 10:25 .project_x.txt
drwx--x--- 2 researcher2 research_team 4096 Nov 15 10:30 drafts
-rw-rw-r-- 1 researcher2 research_team  273 Nov 15 10:20 project_k.txt
-rw-r----- 1 researcher2 research_team  182 Nov 15 10:15 project_m.txt
-rw-rw-r-- 1 researcher2 research_team  104 Nov 15 10:10 project_r.txt
-rw-rw-r-- 1 researcher2 research_team   79 Nov 15 10:05 project_t.txt
```

### Explanation:

The `ls` command lists directory contents, while the `-la` options provide:
- `-l`: Long format listing showing permissions, owner, group, size, and modification date
- `-a`: Displays all files, including hidden files (those starting with `.`)

This command reveals all files and subdirectories in the `/home/researcher2/projects` directory along with their detailed permission settings.

---

## Describe the Permissions String

Let's analyze the permissions string for the file `project_t.txt`:

```
-rw-rw-r--
```

### 10-Character String Breakdown:

The 10-character string represents the file's permissions and type:

| Position | Character | Meaning |
|----------|-----------|---------|
| 1 | `-` | **File type**: `-` indicates a regular file (would be `d` for directory, `l` for symbolic link) |
| 2-4 | `rw-` | **User (owner) permissions**: read (`r`), write (`w`), no execute (`-`) |
| 5-7 | `rw-` | **Group permissions**: read (`r`), write (`w`), no execute (`-`) |
| 8-10 | `r--` | **Other permissions**: read only (`r`), no write (`-`), no execute (`-`) |

### Permission Details:

- **User (researcher2)**: Can read and modify the file
- **Group (research_team)**: Can read and modify the file
- **Other (everyone else)**: Can only read the file

---

## Change File Permissions

### Issue Identified:

The organization's security policy does not allow "other" (users outside the owner and group) to have write access to any files. Upon reviewing the output, I identified that the file `project_k.txt` has write permissions for "other":

```
-rw-rw-rw-  project_k.txt
```

The last `w` in the permissions string (`rw-`) indicates that "other" has write access, which violates the security policy.

### Command Used to Remove Write Access:

```bash
chmod o-w project_k.txt
```

### Verification Command:

```bash
ls -l project_k.txt
```

### Updated Output:

```
-rw-rw-r-- 1 researcher2 research_team 273 Nov 15 10:20 project_k.txt
```

### Explanation:

The `chmod` command changes file permissions:
- `o`: Refers to "other" (all users not in the owner or group)
- `-w`: Removes (`-`) write (`w`) permission
- `project_k.txt`: The target file

After executing this command, "other" users can only read the file, not modify it. The permissions changed from `-rw-rw-rw-` to `-rw-rw-r--`, successfully removing unauthorized write access while maintaining read access for transparency.

---

## Change File Permissions on a Hidden File

### Issue Identified:

The research team has archived `.project_x.txt` (indicated by the leading `.` in the filename, making it a hidden file). According to the security requirements:
- The file should not have write permissions for anyone
- The user and group should be able to read the file

Current permissions:
```
-rw--w---- .project_x.txt
```

Issues:
- User has write permission (should be removed)
- Group has only write permission but should have read permission instead
- Neither user nor group can read the file

### Command Used:

```bash
chmod u-w,g-w,g+r .project_x.txt
```

### Verification Command:

```bash
ls -la .project_x.txt
```

### Updated Output:

```
-r--r----- 1 researcher2 research_team 46 Nov 15 10:25 .project_x.txt
```

### Explanation:

The `chmod` command was used with multiple permission changes separated by commas:
- `u-w`: Removes write permission for the user (owner)
- `g-w`: Removes write permission for the group
- `g+r`: Adds read permission for the group

The result: Both user and group can now read the archived file, but no one can modify it, which is appropriate for an archived document. The write permissions have been removed from all categories, making the file read-only for authorized users.

---

## Change Directory Permissions

### Issue Identified:

The `drafts` directory belongs to `researcher2`, and only `researcher2` should be allowed to access this directory and its contents.

Current permissions:
```
drwx--x--- drafts
```

Issue:
- Group has execute permission (`x`), allowing group members to access the directory
- Group should have no permissions at all

### Command Used:

```bash
chmod g-x drafts
```

### Verification Command:

```bash
ls -ld drafts
```

### Updated Output:

```
drwx------ 2 researcher2 research_team 4096 Nov 15 10:30 drafts
```

### Explanation:

The `chmod` command modified the directory permissions:
- `g-x`: Removes execute permission for the group

For directories, the execute permission (`x`) allows users to access (enter) the directory. By removing this permission from the group, we ensure that only `researcher2` (the owner) can access the `drafts` directory and its contents.

The updated permissions `drwx------` indicate:
- User: Full access (read, write, execute)
- Group: No access
- Other: No access (was already restricted)

This implements the principle of least privilege by ensuring only the authorized user can access sensitive draft documents.

---

## Summary

This project demonstrated my ability to manage Linux file permissions to enforce security policies and implement the principle of least privilege. I successfully:

1. **Audited file and directory permissions** using `ls -la` to identify security policy violations
2. **Removed unauthorized write access** from `project_k.txt` to comply with organizational policy preventing "other" users from modifying files
3. **Secured archived file** `.project_x.txt` by removing write permissions from all users while ensuring appropriate read access for user and group
4. **Restricted directory access** to the `drafts` directory by removing group execute permissions, ensuring only researcher2 can access sensitive draft materials

These actions ensured that all files and directories in the research team's projects folder have appropriate authorization levels, reducing the risk of unauthorized access or modifications. By using Linux command-line tools effectively, I maintained system security while preserving necessary access for legitimate users.

### Linux Commands Used:
- `ls -la`: List all files including hidden ones with detailed permissions
- `ls -l`: List files with detailed information
- `ls -ld`: List directory information (not contents)
- `chmod`: Change file and directory permissions
  - `u`, `g`, `o`: User, group, other
  - `+`, `-`: Add, remove permissions
  - `r`, `w`, `x`: Read, write, execute

### Security Principles Applied:
- **Least Privilege**: Users granted minimum necessary permissions
- **Access Control**: Proper authorization aligned with job functions
- **Defense in Depth**: Multiple layers of permission restrictions
- **Regular Auditing**: Systematic review of access permissions

This project showcases my practical skills in Linux system administration and security, demonstrating my ability to protect organizational assets through proper access controls.

---

**Project Completed By**: [Your Name]
**Date**: [Current Date]
**Organization**: Research Team Security Management
