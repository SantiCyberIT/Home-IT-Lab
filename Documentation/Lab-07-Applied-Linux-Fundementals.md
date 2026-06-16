# Linux User, Group, Ownership, and Permissions Lab

## Project Overview

This project demonstrates fundamental Linux system administration skills including:

* User creation
* Group management
* Group membership assignment
* Directory creation
* Ownership management
* Group ownership management
* Linux permissions
* Access control testing
* Troubleshooting permission issues
* Organizing shared company resources using Linux best practices

The environment simulates a fictional company called **SantiCyberCo** with multiple departments and employees.

---

# Company Structure

## IT Department

### Manager

* Gary

### Employees

* Sue
* John

### Group

* IT

### Department Resources

* IT_logs
* IT_tickets

---

## Accounting Department

### Manager

* Sally

### Employees

* Harry
* Carol

### Group

* Accounting

### Department Resources

* Accounting_logs
* Finance_logs

---

## Human Resources Department

### Manager

* James

### Employees

* Beth
* Charles

### Group

* HR

### Department Resources

* HR_records
* HR_profiles

---

# Objectives

Configure a Linux environment where:

* Managers own department directories.
* Employees gain access through department groups.
* Users outside the department are denied access.
* Permissions follow the principle of least privilege.
* Company data is stored in a shared location rather than a personal user directory.

---

# Step 1 - Create Users

Managers:

```bash
sudo adduser gary
sudo adduser sally
sudo adduser james
```

Employees:

```bash
sudo adduser sue
sudo adduser john

sudo adduser harry
sudo adduser carol

sudo adduser beth
sudo adduser charles
```

Verify users:

```bash
id gary
id sally
id james

id sue
id john

id harry
id carol

id beth
id charles
```

---

# Step 2 - Create Department Groups

```bash
sudo groupadd IT
sudo groupadd Accounting
sudo groupadd HR
```

Verify:

```bash
cat /etc/group | grep IT
cat /etc/group | grep Accounting
cat /etc/group | grep HR
```

---

# Step 3 - Add Users to Groups

## IT

```bash
sudo usermod -aG IT gary
sudo usermod -aG IT sue
sudo usermod -aG IT john
```

## Accounting

```bash
sudo usermod -aG Accounting sally
sudo usermod -aG Accounting harry
sudo usermod -aG Accounting carol
```

## HR

```bash
sudo usermod -aG HR james
sudo usermod -aG HR beth
sudo usermod -aG HR charles
```

Verify:

```bash
groups gary
groups sue
groups john

groups sally
groups harry
groups carol

groups james
groups beth
groups charles
```

---

# Step 4 - Create Department Directories

```bash
mkdir IT_logs
mkdir IT_tickets

mkdir Accounting_logs
mkdir Finance_logs

mkdir HR_records
mkdir HR_profiles
```

Verify:

```bash
ls
```

---

# Step 5 - Assign Ownership

## IT

```bash
sudo chown gary IT_logs
sudo chown gary IT_tickets
```

## Accounting

```bash
sudo chown sally Accounting_logs
sudo chown sally Finance_logs
```

## HR

```bash
sudo chown james HR_records
sudo chown james HR_profiles
```

---

# Step 6 - Assign Group Ownership

## IT

```bash
sudo chgrp IT IT_logs
sudo chgrp IT IT_tickets
```

## Accounting

```bash
sudo chgrp Accounting Accounting_logs
sudo chgrp Accounting Finance_logs
```

## HR

```bash
sudo chgrp HR HR_records
sudo chgrp HR HR_profiles
```

Verify:

```bash
ls -ld *
```

---

# Step 7 - Configure Permissions

Permissions were configured using:

```bash
sudo chmod 750 IT_logs
sudo chmod 750 IT_tickets

sudo chmod 750 Accounting_logs
sudo chmod 750 Finance_logs

sudo chmod 750 HR_records
sudo chmod 750 HR_profiles
```

### Permission Breakdown

| Value | Permission |
| ----- | ---------- |
| 4     | Read       |
| 2     | Write      |
| 1     | Execute    |

### 750 Means

Owner:

```text
rwx
```

Group:

```text
r-x
```

Others:

```text
---
```

Result:

```text
drwxr-x---
```

---

# Step 8 - Test Access Controls

Logged in as Sue:

```bash
su - sue
```

Attempted to access department folders.

Initially received:

```text
Permission denied
```

This appeared incorrect because Sue was a member of the IT group.

Further troubleshooting revealed that the issue was not the IT directory permissions.

The problem was that the project existed inside:

```text
/home/santicyber/Desktop/SantiCyberCo
```

Linux requires execute permissions on every directory in the path.

Because the company data was stored inside another user's Desktop directory, department users could not traverse the entire path.

---

# Troubleshooting and Lessons Learned

## Mistake #1 - Forgot sudo

Attempted:

```bash
chmod 750 IT_logs
```

Received:

```text
Operation not permitted
```

Cause:

The directory owner was Gary, not the current user.

Correction:

```bash
sudo chmod 750 IT_logs
```

---

## Mistake #2 - Wrong Working Directory

Attempted:

```bash
ls -ld IT_logs
```

Received:

```text
No such file or directory
```

Cause:

The command was executed from the wrong directory.

Correction:

```bash
pwd
cd correct_directory
```

Always verify current location before troubleshooting.

---

## Mistake #3 - Typographical Error

Attempted:

```bash
ls -ld HR_logs
```

Received:

```text
No such file or directory
```

Actual directory name:

```text
HR_records
```

---

## Mistake #4 - Permission Denied Investigation

Sue was denied access to all department folders.

Investigation showed:

```bash
groups
```

confirmed Sue belonged to IT.

Further checks:

```bash
ls -ld /home
ls -ld /home/santicyber
ls -ld /home/santicyber/Desktop
ls -ld /home/santicyber/Desktop/SantiCyberCo
```

revealed the issue was directory traversal permissions.

This led to relocating the company resources.

---

# Step 9 - Move Company Data to Shared Service Directory

A discussion was held regarding Linux best practices.

Original location:

```text
/home/santicyber/Desktop/SantiCyberCo
```

Improved location:

```text
/serv/SantiCyberCo
```

Correction:

```text
/srv/SantiCyberCo
```

The `/srv` directory is commonly used for shared service data.

Verify `/srv`:

```bash
ls -ld /srv
```

Move project:

```bash
sudo mv /home/santicyber/Desktop/SantiCyberCo /srv/
```

Verify:

```bash
ls /srv/SantiCyberCo
```

Output:

```text
Accounting_logs
Finance_logs
HR_profiles
HR_records
IT_logs
IT_tickets
```

---

# Final Verification

```bash
cd /srv/SantiCyberCo

ls -ld *
```

Results:

```text
drwxr-x--- gary  IT         IT_logs
drwxr-x--- gary  IT         IT_tickets

drwxr-x--- sally Accounting Accounting_logs
drwxr-x--- sally Accounting Finance_logs

drwxr-x--- james HR         HR_records
drwxr-x--- james HR         HR_profiles
```

---

# Final Directory Structure

```text
/srv/SantiCyberCo
├── Accounting_logs
├── Finance_logs
├── HR_profiles
├── HR_records
├── IT_logs
└── IT_tickets
```

---

# Skills Demonstrated

* Linux user administration
* Linux group administration
* Role-Based Access Control (RBAC)
* File ownership management
* Group ownership management
* Permission management
* chmod
* chown
* chgrp
* Directory traversal permissions
* Linux filesystem hierarchy
* Troubleshooting permission issues
* Verifying changes using Linux commands
* Applying least-privilege security principles

---

# Key Takeaways

This project reinforced the importance of:

* Verifying group membership
* Understanding directory traversal permissions
* Using least privilege
* Storing shared resources in appropriate Linux directories
* Troubleshooting methodically rather than assuming the first error message reveals the root cause

The most valuable lesson learned was that permissions on the target directory are only part of the equation. Users must also have sufficient permissions to traverse every directory in the path leading to that resource.

