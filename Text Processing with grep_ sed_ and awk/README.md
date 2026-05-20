# Text Processing with grep, sed, and awk

A comprehensive lab guide designed to teach core Linux text-processing tools, regular expressions, and advanced stream manipulation techniques.

---

## 🎯 Objectives
By the end of this lab, you will be able to:
* **Use** `grep` to search for patterns and text within files and directories.
* **Apply** `sed` (stream editor) to perform text substitution and transformations.
* **Create** and execute `awk` scripts for advanced text extraction, manipulation, and reporting.
* **Combine** these three tools to solve real-world data processing challenges.
* **Understand** regular expressions and pattern matching in Linux environments.
* **Process** log files and structured data efficiently using command-line utilities.

---

## 🛠️ Prerequisites & Setup

### Prerequisites
Before starting, ensure you have:
* Basic knowledge of Linux command line navigation (`cd`, `ls`, `cat`, etc.).
* Understanding of file system structure and file permissions.
* Familiarity with command-line text editors like `nano` or `vim`.
* Completed basic foundational Linux terminal labs.

### Lab Environment Setup
This lab is optimized for **Al Nafi Cloud Machines** (CentOS/RHEL-based) with `grep`, `sed`, and `awk` pre-installed, along with practice sample data files and root access.

---

## 🚀 Lab Tasks

### Task 1: Search Text in Files Using `grep`

#### 1.1 Create Test Files and Workspace
```bash
# Create and navigate to the working directory
mkdir ~/text_processing_lab
cd ~/text_processing_lab

# Create sample structured employee records
cat > employees.txt << 'EOF'
John Smith,Manager,Sales,75000
Jane Doe,Developer,IT,65000
Mike Johnson,Analyst,Finance,55000
Sarah Wilson,Manager,IT,80000
Tom Brown,Developer,IT,60000
Lisa Davis,Analyst,Sales,50000
Robert Taylor,Manager,Finance,85000
Emily White,Developer,IT,62000
David Lee,Analyst,IT,58000
Maria Garcia,Manager,Sales,78000
EOF

# Create sample server application logs
cat > server_logs.txt << 'EOF'
2024-01-15 10:30:15 INFO: Server started successfully
2024-01-15 10:31:22 ERROR: Database connection failed
2024-01-15 10:32:10 WARNING: High memory usage detected
2024-01-15 10:33:45 INFO: User login: admin
2024-01-15 10:34:12 ERROR: File not found: /var/log/app.log
2024-01-15 10:35:30 INFO: Backup process completed
2024-01-15 10:36:18 WARNING: Disk space low
2024-01-15 10:37:25 ERROR: Network timeout occurred
2024-01-15 10:38:40 INFO: User logout: admin
2024-01-15 10:39:55 INFO: System maintenance scheduled
EOF

# Create sample comma-separated product inventory data
cat > products.txt << 'EOF'
Laptop,Electronics,999.99,50
Mouse,Electronics,29.99,200
Keyboard,Electronics,79.99,150
Chair,Furniture,299.99,25
Desk,Furniture,499.99,15
Monitor,Electronics,399.99,75
Headphones,Electronics,149.99,100
Lamp,Furniture,89.99,40
Notebook,Office,12.99,500
Pen,Office,2.99,1000
EOF
```

#### 1.2 Basic `grep` Operations
```bash
# Search for a specific word
grep "Manager" employees.txt

# Search case-insensitively
grep -i "manager" employees.txt

# Count lines containing the pattern
grep -c "IT" employees.txt

# Show line numbers alongside matching text
grep -n "Developer" employees.txt

# Invert match (show lines that do not contain 'IT')
grep -v "IT" employees.txt
```

#### 1.3 Advanced `grep` with Regular Expressions
```bash
# Match lines starting with a timestamp range (10:30 to 10:35)
grep "^2024-01-15 10:3[0-5]" server_logs.txt

# Match lines ending with '000'
grep "000\$" employees.txt

# Match lines containing any numeric digits
grep "[0-9]" products.txt

# Use Extended Regex (ERE) to search for alternate options
grep -E "(ERROR|WARNING)" server_logs.txt

# Search recursively within a directory structures
mkdir logs
cp server_logs.txt logs/
grep -r "ERROR" .
```

#### 1.4 Practical `grep` Pipeline Examples
```bash
# Find all employees with a salary from 70,000 to 99,999
grep -E ",[7-9][0-9][0-9][0-9][0-9]\$" employees.txt

# Filter out all items in the Electronics category
grep "Electronics" products.txt

# Isolate logs captured between 10:30 and 10:35
grep "10:3[0-5]" server_logs.txt

# Pipe multiple filters together (IT Managers)
grep "IT" employees.txt | grep "Manager"
```

---

### Task 2: Use `sed` for Text Replacement

#### 2.1 Basic Text Substitution
```bash
# Substitute the first occurrence per line
sed 's/IT/Information Technology/' employees.txt

# Globally substitute all occurrences per line
sed 's/IT/Information Technology/g' employees.txt

# Save substitutions directly to a new file
sed 's/IT/Information Technology/g' employees.txt > employees_updated.txt

# Edit file content in place safely with a backup copy
cp employees.txt employees_backup.txt
sed -i 's/IT/Information Technology/g' employees_backup.txt
cat employees_backup.txt
```

#### 2.2 Advanced Line-Targeted `sed` Operations
```bash
# Delete line 2 from the target stream
sed '2d' employees.txt

# Delete lines containing the pattern "Manager"
sed '/Manager/d' employees.txt

# Insert text at specific absolute block boundaries (1st line and last line)
sed '1i\Employee Database Report' employees.txt  # Insert above Line 1
sed '\$a\End of Report' employees.txt            # Append below final line

# Target transformations exclusively at specified line conditions
sed '3s/Analyst/Senior Analyst/' employees.txt
sed '/Finance/s/Analyst/Senior Analyst/' employees.txt
```

#### 2.3 Complex `sed` Transformations
```bash
# Chain multiple modification expressions into a single pass
sed -e 's/IT/Information Technology/g' -e 's/Manager/Director/g' employees.txt

# Redact all 5-digit salary blocks using regular expressions
sed 's/[0-9][0-9][0-9][0-9][0-9]/SALARY_HIDDEN/g' employees.txt

# Uniformly format varying log severity patterns
sed 's/ERROR:/[ERROR]:/g; s/WARNING:/[WARNING]:/g; s/INFO:/[INFO]:/' server_logs.txt

# Reorder or extract text fragments using captured groups
sed 's/.*,\([^,]*\),\([^,]*\),.*/Department: \2, Role: \1/' employees.txt
```

#### 2.4 Practical Multi-Command `sed` Examples
```bash
# Build a stylized employee file summary report
sed -e '1i\=== EMPLOYEE REPORT ===' \
    -e 's/,/ | /g' \
    -e '\$a\=== END OF REPORT ===' employees.txt

# Clean details and clip historical date stamps from logs
sed -e 's/2024-01-15 //' \
    -e 's/INFO:/[INFO]/' \
    -e 's/ERROR:/[ERROR]/' \
    -e 's/WARNING:/[WARN]/' server_logs.txt

# Transform raw CSV lines into pipe-delimited data structures
sed 's/,/|/g' products.txt
```

---

### Task 3: Create and Use `awk` Scripts

#### 3.1 Basic Field-Level Text Isolation
```bash
# Extract and print individual fields using custom delimiter
awk -F',' '{print \$1}' employees.txt
awk -F',' '{print \$1, \$2}' employees.txt

# Decorate outputs inline alongside raw target columns
awk -F',' '{print "Name: " \$1 ", Position: " \$2}' employees.txt

# Enumerate records output using the Record Number (NR) variable
awk -F',' '{print NR ": " \$1}' employees.txt

# Summarize full line metadata during final stream block processing
awk 'END {print "Total employees: " NR}' employees.txt
```

#### 3.2 Programmed Conditions and Dynamic Filters
```bash
# Print explicit fields if column 2 strictly matches 'Manager'
awk -F',' '\$2 == "Manager" {print \$1, \$4}' employees.txt

# Isolate numeric columns against logical expression values (> 60000)
awk -F',' '\$4 > 60000 {print \$1, \$4}' employees.txt

# Filter out specific staff segments by explicit department name
awk -F',' '\$3 == "IT" {print \$1, \$2}' employees.txt

# Aggregate and print associative arrays dynamically at the final block step
awk -F',' '{dept[\$3]++} END {for (d in dept) print d, dept[d]}' employees.txt
```

#### 3.3 Structuring Complex `awk` Scripts
Create an executable automation script for processing database files comprehensively:

```bash
cat > employee_analysis.awk << 'EOF'
BEGIN {
    FS = ","
    print "=== EMPLOYEE ANALYSIS REPORT ==="
    print "================================="
    total_salary = 0
    employee_count = 0
    max_salary = 0
    highest_paid = ""
}

{
    # Count employees by department and position
    dept[\$3]++
    position[\$2]++
    
    # Track financial aggregates
    total_salary += \$4
    employee_count++
    
    # Calculate conditional bounds
    if (\$4 > max_salary) {
        max_salary = \$4
        highest_paid = \$1
    }
}

END {
    print "Total Employees: " employee_count
    print "Average Salary : \$" (total_salary / employee_count)
    print "Highest Earner : " highest_paid " (\$" max_salary ")"
    print ""
    print "--- Breakdown by Department ---"
    for (d in dept) {
        print d ": " dept[d] " employee(s)"
    }
}
EOF

# Execute your script using raw input data files
awk -f employee_analysis.awk employees.txt
```

---

## 🛡️ Best Practices
* **Quote Patterns Intentionally:** Always wrap your `sed` and `awk` operations in single quotes (`'...'`) to prevent local bash shells from expanding special characters like `$1` or `*`.
* **Dry-Run Mode Safety:** Never default to using `sed -i` (in-place modification) directly. Test transformations safely over your shell outputs or route modifications to temporary preview files first.
* **Match Boundaries Closely:** Use explicit anchors like `^` (start of line) and `$` (end of line) in your expressions to avoid match corruption errors on complex structured documents.
