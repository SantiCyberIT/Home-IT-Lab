# Lab 03 - Linux File Management

## Objective

Learn basic Linux file and directory management using the command line.

## Environment

- Ubuntu Linux
- VirtualBox
- User Account: santicyber

---

## Tasks Performed

### 1. Created a File

Command:

```bash
touch lab3.txt
```

Verified:

```bash
ls
```

Result:

```text
lab3.txt
```

---

### 2. Edited a File Using Nano

Opened file:

```bash
nano lab3.txt
```

Added text:

```text
This is my first file created with nano!
```

Saved using:

```text
Ctrl + O
Enter
```

Exited using:

```text
Ctrl + X
```

Verified contents:

```bash
cat lab3.txt
```

Output:

```text
This is my first file created with nano!
```

---

### 3. Created a Directory

Initial attempt:

```bash
mkdir Practice Folder
```

Result:

Linux created two separate directories:

```text
Practice
Folder
```

Reason:

Spaces separate command arguments in Linux.

Corrected by deleting the directories:

```bash
rm -r Practice
rm -r Folder
```

Created the directory correctly:

```bash
mkdir PracticeFolder
```

Verified:

```bash
ls
```

Output:

```text
PracticeFolder
```

---

### 4. Navigated Directories

Moved into the directory:

```bash
cd PracticeFolder
```

Verified location:

```bash
pwd
```

Output:

```text
/home/santicyber/Desktop/PracticeFolder
```

---

### 5. Created a File Inside a Directory

Command:

```bash
touch notes.txt
```

Verified:

```bash
ls
```

Output:

```text
notes.txt
```

---

### 6. Edited and Saved File

Opened file:

```bash
nano notes.txt
```

Added text:

```text
This file was created inside a folder I created!
```

Saved:

```text
Ctrl + O
Enter
```

Exited:

```text
Ctrl + X
```

Verified:

```bash
cat notes.txt
```

Output:

```text
This file was created inside a folder I created!
```

---

### 7. Copied a File

Initial mistake:

```bash
cp notes.text backup.txt
```

Error:

```text
No such file or directory
```

Cause:

Typed:

```text
notes.text
```

instead of:

```text
notes.txt
```

Correct command:

```bash
cp notes.txt backup.txt
```

Verified:

```bash
ls
```

Output:

```text
backup.txt
notes.txt
```

---

### 8. Renamed a File

Command:

```bash
mv backup.txt backup-old.txt
```

Verified:

```bash
ls
```

Output:

```text
backup-old.txt
notes.txt
```

---

## Commands Learned

```bash
pwd
ls
cd
mkdir
touch
nano
cat
cp
mv
rm -r
```

---

## Lessons Learned

- Linux commands are case-sensitive.
- Spaces separate command arguments.
- File extensions must be typed exactly.
- Linux often produces no output when a command succeeds.
- Error messages provide useful troubleshooting information.
- `cp` copies files.
- `mv` renames or moves files.
- `rm -r` removes directories and their contents.
- `pwd` shows the current working directory.

---

## Screenshots

Stored in:

Screenshots/Lab-03/

