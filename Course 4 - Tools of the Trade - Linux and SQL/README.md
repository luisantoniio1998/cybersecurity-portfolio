# Course 4: Tools of the Trade - Linux and SQL

## Overview
Linux operating system, command line interface, file permissions, and SQL database queries.

## Portfolio Activities

### Activity 1: File Permissions in Linux
- **Description**: Manage file and directory permissions using Linux commands
- **Skills**: Linux CLI, chmod, principle of least privilege, access control
- **File**: [`file-permissions-linux.md`](./file-permissions-linux.md)
- **What's Included**:
  - Audit current file permissions with `ls -la`
  - Remove unauthorized write access
  - Secure archived files (read-only)
  - Restrict directory access
  - Apply least privilege principle

### Activity 2: SQL Queries for Security Investigation
- **Description**: Use SQL queries with filters to investigate security incidents
- **Skills**: SQL SELECT, WHERE clauses, filtering, AND/OR/NOT operators, LIKE patterns
- **File**: [`sql-queries-investigation.md`](./sql-queries-investigation.md)
- **What's Included**:
  - Retrieve after-hours failed login attempts
  - Analyze login attempts on specific dates
  - Filter login attempts by location
  - Identify employees for security updates by department/office

## Key Learnings
- Linux command line navigation
- File permissions and ownership (chmod, chown)
- User and group management
- SQL SELECT, WHERE, JOIN operations
- Database security queries
- Log file analysis with Linux tools

## Tools Used
- Linux/Bash
- SQL (MariaDB/MySQL)
- grep, awk, sed
- chmod, chown, ls
