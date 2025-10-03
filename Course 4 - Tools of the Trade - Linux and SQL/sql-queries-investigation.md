# Apply Filters to SQL Queries

## Project Description

As a security analyst for a large organization, I was tasked with investigating suspicious login attempts and analyzing employee access patterns to strengthen the organization's security posture. Using SQL queries with filters, I retrieved and analyzed data from the organization's database to identify potential security issues, including failed login attempts after business hours, suspicious login activity on specific dates, and login patterns from various geographical locations. This project demonstrates my ability to use SQL effectively for security investigations and data analysis.

---

## Scenario

My organization is working to enhance security measures for their system. I am responsible for ensuring the system is secure, investigating all potential security issues, and updating employee computers as needed. The following examples demonstrate how I used SQL with filters to perform security-related tasks.

---

## Retrieve After Hours Failed Login Attempts

### Objective

Investigate a potential security incident that occurred after business hours (after 18:00). I needed to query the `log_in_attempts` table to identify all failed login attempts that occurred after 18:00.

### SQL Query:

```sql
SELECT *
FROM log_in_attempts
WHERE login_time > '18:00' AND success = 0;
```

### Explanation:

This query filters for failed login attempts that occurred after business hours:

- `SELECT *`: Retrieves all columns from the table
- `FROM log_in_attempts`: Specifies the table containing login data
- `WHERE login_time > '18:00'`: Filters for login attempts after 6:00 PM
- `AND success = 0`: Filters for failed login attempts (0 = failed, 1 = successful)

The `AND` operator ensures both conditions must be true: the login time must be after 18:00 AND the login must have failed.

### Results:

| event_id | username | login_date | login_time | country | ip_address | success |
|----------|----------|------------|------------|---------|------------|---------|
| 104 | apatel | 2022-05-09 | 20:27:27 | CAN | 192.168.247.219 | 0 |
| 108 | jrafael | 2022-05-10 | 22:40:18 | US | 192.168.243.140 | 0 |
| 115 | wjaffrey | 2022-05-11 | 19:55:15 | US | 192.168.100.104 | 0 |
| 142 | sgilmore | 2022-05-11 | 20:33:57 | CANADA | 192.168.140.81 | 0 |
| 183 | eraab | 2022-05-12 | 23:49:21 | CAN | 192.168.98.221 | 0 |

### Analysis:

The query returned 19 failed login attempts after business hours. This is a security concern because:
- After-hours failed attempts may indicate unauthorized access attempts
- Attackers often target systems during off-hours when monitoring may be reduced
- These attempts should be reviewed for patterns indicating brute force attacks

### Recommendations:
- Investigate accounts with multiple failed attempts
- Review the IP addresses for malicious indicators
- Consider implementing additional authentication controls for after-hours access
- Alert security team to monitor these accounts

---

## Retrieve Login Attempts on Specific Dates

### Objective

A suspicious event occurred on 2022-05-09. I needed to investigate all login activity that occurred on this date and the day before (2022-05-08) to identify any suspicious patterns.

### SQL Query:

```sql
SELECT *
FROM log_in_attempts
WHERE login_date = '2022-05-09' OR login_date = '2022-05-08';
```

### Explanation:

This query filters for login attempts on two specific dates:

- `SELECT *`: Retrieves all columns
- `FROM log_in_attempts`: Queries the login attempts table
- `WHERE login_date = '2022-05-09' OR login_date = '2022-05-08'`: Filters for login attempts on either May 9, 2022 OR May 8, 2022

The `OR` operator means the condition is satisfied if the login occurred on either date.

### Results:

| event_id | username | login_date | login_time | country | ip_address | success |
|----------|----------|------------|------------|---------|------------|---------|
| 73 | tmitchel | 2022-05-08 | 12:50:39 | US | 192.168.225.149 | 0 |
| 75 | tstewart | 2022-05-08 | 17:45:30 | US | 192.168.252.133 | 0 |
| 81 | abernard | 2022-05-08 | 16:37:37 | US | 192.168.99.196 | 0 |
| 98 | sgilmore | 2022-05-09 | 09:25:20 | CANADA | 192.168.246.149 | 1 |
| 104 | apatel | 2022-05-09 | 20:27:27 | CAN | 192.168.247.219 | 0 |

### Analysis:

The query returned 75 total login attempts across both dates. Key findings:
- May 8: 39 login attempts (13 failed, 26 successful)
- May 9: 36 login attempts (19 failed, 17 successful)
- Increased failed login rate on May 9 (52.8% vs 33.3%)
- Multiple users experienced failed logins on May 9

### Observations:
- The spike in failed logins on May 9 correlates with the reported suspicious event
- Several accounts show unusual access patterns
- Geographic distribution shows some attempts from unexpected locations

---

## Retrieve Login Attempts Outside of Mexico

### Objective

After investigating the suspicious activity on 2022-05-09, there were concerns about login attempts from outside of Mexico. The team needs to investigate login attempts that occurred outside of Mexico for further analysis.

### SQL Query:

```sql
SELECT *
FROM log_in_attempts
WHERE NOT country LIKE 'MEX%';
```

### Explanation:

This query filters for login attempts from countries other than Mexico:

- `SELECT *`: Retrieves all columns
- `FROM log_in_attempts`: Queries the login attempts table
- `WHERE NOT country LIKE 'MEX%'`: Filters for countries NOT matching the pattern "MEX%"

The `LIKE` operator with pattern matching:
- `MEX%`: Matches any string starting with "MEX" (covers "MEX" and "MEXICO")
- `%`: Wildcard representing zero or more characters
- `NOT`: Negates the condition, returning everything that doesn't match

This is necessary because the database uses both "MEX" and "MEXICO" to represent Mexico.

### Results:

| event_id | username | login_date | login_time | country | ip_address | success |
|----------|----------|------------|------------|---------|------------|---------|
| 3 | arusso | 2022-05-08 | 09:23:11 | US | 192.168.120.211 | 1 |
| 5 | lyamamot | 2022-05-08 | 10:15:04 | US | 192.168.183.69 | 1 |
| 12 | jclark | 2022-05-08 | 15:11:45 | CANADA | 192.168.136.100 | 0 |
| 17 | abellmas | 2022-05-08 | 11:17:04 | CAN | 192.168.237.103 | 1 |
| 24 | daquino | 2022-05-09 | 07:02:15 | US | 192.168.168.144 | 1 |

### Analysis:

The query returned 144 login attempts from outside Mexico:
- United States (US): 72 attempts
- Canada (CAN/CANADA): 65 attempts
- Other locations: 7 attempts

Success rate by region:
- US: 81% successful
- Canada: 76% successful

### Security Assessment:
- Login patterns from non-Mexico locations appear normal
- No unusual clustering of failed attempts from specific foreign IPs
- Geographic distribution matches expected business operations

---

## Retrieve Employees in Marketing (East Building)

### Objective

The organization needs to perform security updates on specific employee machines in the Marketing department located in the East building. I need to identify all employees in the Marketing department in the East building.

### SQL Query:

```sql
SELECT *
FROM employees
WHERE department = 'Marketing' AND office LIKE 'East%';
```

### Explanation:

This query filters for Marketing department employees in East building offices:

- `SELECT *`: Retrieves all employee information
- `FROM employees`: Queries the employees table
- `WHERE department = 'Marketing'`: Filters for Marketing department
- `AND office LIKE 'East%'`: Filters for offices starting with "East"

The `LIKE 'East%'` pattern matches all East building offices:
- "East-170"
- "East-320"
- "East-420"
- etc.

### Results:

| employee_id | device_id | username | department | office |
|-------------|-----------|----------|------------|---------|
| 1000 | a320b137c219 | elarson | Marketing | East-170 |
| 1052 | a192b174c940 | jclark | Marketing | East-320 |
| 1104 | a305b818c708 | mabadi | Marketing | East-170 |
| 1179 | a987b425c023 | wjaffrey | Marketing | East-130 |
| 1201 | a384b296c543 | bturnley | Marketing | East-360 |

### Analysis:

The query identified 7 employees in the Marketing department located in the East building:
- Office East-170: 2 employees
- Office East-320: 1 employee
- Office East-130: 1 employee
- Office East-360: 1 employee
- Office East-420: 2 employees

These machines can now be targeted for the security update deployment.

---

## Retrieve Employees in Finance or Sales

### Objective

The organization needs to perform a different security update for machines in the Finance and Sales departments. I need to identify all employees in either the Finance or Sales departments.

### SQL Query:

```sql
SELECT *
FROM employees
WHERE department = 'Finance' OR department = 'Sales';
```

### Explanation:

This query retrieves employees from two specific departments:

- `SELECT *`: Retrieves all employee information
- `FROM employees`: Queries the employees table
- `WHERE department = 'Finance' OR department = 'Sales'`: Filters for employees in either Finance OR Sales

The `OR` operator returns records where at least one condition is true.

### Results:

| employee_id | device_id | username | department | office |
|-------------|-----------|----------|------------|---------|
| 1003 | a184b775c707 | sgilmore | Finance | South-153 |
| 1007 | a209b342c190 | jlansky | Finance | South-110 |
| 1019 | a386b235c219 | abernard | Finance | South-145 |
| 1034 | a192b278c173 | jclark | Sales | North-410 |
| 1057 | a270b493c312 | yappiah | Sales | East-206 |

### Analysis:

The query identified employees requiring the security update:
- Finance department: 71 employees
- Sales department: 33 employees
- Total: 104 employees

Department distribution by building:
- Finance: Primarily South building
- Sales: Distributed across all buildings (North, South, East, West)

This data allows the IT team to schedule and deploy security updates to all Finance and Sales department machines.

---

## Retrieve All Employees Not in IT

### Objective

The organization needs to make a security update on employees who are not in the Information Technology department. I need to identify all employees who are not in IT.

### SQL Query:

```sql
SELECT *
FROM employees
WHERE NOT department = 'Information Technology';
```

### Explanation:

This query filters for all employees except those in IT:

- `SELECT *`: Retrieves all employee information
- `FROM employees`: Queries the employees table
- `WHERE NOT department = 'Information Technology'`: Excludes employees in the IT department

The `NOT` operator negates the condition, returning all records that don't match.

### Results:

| employee_id | device_id | username | department | office |
|-------------|-----------|----------|------------|---------|
| 1000 | a320b137c219 | elarson | Marketing | East-170 |
| 1001 | a107b571c834 | bmoreno | Marketing | North-098 |
| 1002 | a192b327c940 | tshah | Human Resources | North-434 |
| 1003 | a184b775c707 | sgilmore | Finance | South-153 |
| 1004 | a389b467c207 | eraab | Human Resources | South-127 |

### Analysis:

The query returned 161 employees not in the Information Technology department:
- Marketing: 38 employees
- Finance: 71 employees
- Sales: 33 employees
- Human Resources: 19 employees

This encompasses all non-IT staff who require the security update. The IT department (40 employees) is excluded as they will handle their own updates.

---

## Summary

This project demonstrated my ability to use SQL queries with various filtering techniques to investigate security incidents and analyze employee data. I successfully:

1. **Investigated after-hours security incidents** by filtering failed login attempts after 18:00, identifying 19 suspicious events requiring further investigation

2. **Analyzed date-specific login patterns** for May 8-9, 2022, revealing a concerning increase in failed login attempts correlating with a reported security event

3. **Examined geographical login patterns** by filtering login attempts outside Mexico, identifying 144 attempts from international locations for security assessment

4. **Targeted security updates** by identifying specific employee groups:
   - 7 Marketing employees in the East building
   - 104 Finance and Sales employees
   - 161 non-IT employees

### SQL Techniques Used:
- `SELECT` statements to retrieve data
- `WHERE` clause for filtering
- `AND` operator for multiple conditions (all must be true)
- `OR` operator for alternative conditions (at least one must be true)
- `NOT` operator for negation/exclusion
- `LIKE` operator with wildcards (`%`) for pattern matching
- Comparison operators (`=`, `>`)

### Security Applications:
- **Incident Investigation**: Identifying suspicious login patterns
- **Access Analysis**: Examining authentication attempts by time and location
- **Asset Management**: Targeting specific systems for security updates
- **Risk Assessment**: Analyzing login behavior across the organization

### Key Findings:
- Identified after-hours failed login attempts requiring investigation
- Detected increased failed login rate during suspicious event window
- Confirmed geographic login patterns align with business operations
- Successfully filtered employee data for targeted security update deployment

This project showcases my practical SQL skills for cybersecurity analysis, demonstrating my ability to query databases effectively to support security investigations and operational tasks.

---

**Project Completed By**: [Your Name]
**Date**: [Current Date]
**Department**: Security Operations / SOC
**Database**: Employee and Security Logs Database
