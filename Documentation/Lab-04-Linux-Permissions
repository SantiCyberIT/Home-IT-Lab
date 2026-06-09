# Lab 04 - Linux File Permissions

## Objective

Learn how Linux file permissions work and how to modify them using the `chmod` command.

## Environment

* Ubuntu Linux
* VirtualBox
* User Account: santicyber

## Tasks Performed

### 1. Created a Test File

Command:

```bash
touch secret.txt
```

Verified:

```bash
ls
```

Result:

```text
secret.txt
```

### 2. Viewed Current Permissions

Command:

```bash
ls -l
```

Result:

```text
-rw-rw-r-- secret.txt
```

Observed the permission structure:

* Owner
* Group
* Others

Each permission set contains:

* Read (r)
* Write (w)
* Execute (x)

### 3. Changed Permissions to Full Access

Command:

```bash
chmod 777 secret.txt
```

Verified:

```bash
ls -l secret.txt
```

Result:

```text
-rwxrwxrwx secret.txt
```

Explanation:

* Owner: Read, Write, Execute
* Group: Read, Write, Execute
* Others: Read, Write, Execute

### 4. Changed Permissions to 644

Command:

```bash
chmod 644 secret.txt
```

Verified:

```bash
ls -l secret.txt
```

Result:

```text
-rw-r--r-- secret.txt
```

Explanation:

* Owner: Read, Write
* Group: Read Only
* Others: Read Only

## Concepts Learned

### Permission Values

| Number | Permission |
| ------ | ---------- |
| 4      | Read       |
| 2      | Write      |
| 1      | Execute    |

### Common Permission Combinations

| Value | Permission |
| ----- | ---------- |
| 7     | rwx        |
| 6     | rw-        |
| 5     | r-x        |
| 4     | r--        |
| 0     | ---        |

### Example

```text
755
```

Equals:

```text
Owner  = 7 = rwx
Group  = 5 = r-x
Others = 5 = r-x
```

Result:

```text
rwxr-xr-x
```

## Skills Learned

* Linux file permissions
* Reading permission strings
* Using `chmod`
* Understanding Owner, Group, and Others
* Converting numeric permissions to symbolic permissions
* Basic Linux security concepts

## Evidence

Screenshots for this lab are stored in:

```text
Screenshots/Lab-04/
```
