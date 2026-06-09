# Lab 05 - Linux Users and Groups

## Objective

Learn how Linux identifies users, manages groups, and stores user account information.

---

## Environment

* Ubuntu Linux
* VirtualBox
* User Account: santicyber

---

## Tasks Performed

### 1. Display Current Username

Command:

```bash
whoami
```

Result:

```text
santicyber
```

Observation:

Verified the currently logged-in user account.

---

### 2. Display User and Group Information

Command:

```bash
id
```

Result:

```text
uid=1000(santicyber) gid=1000(santicyber) groups=1000(santicyber),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),100(users),114(lpadmin)
```

Observation:

Displayed the User ID (UID), Primary Group ID (GID), and all group memberships assigned to the user.

---

### 3. Display Group Memberships

Command:

```bash
groups
```

Result:

```text
santicyber adm cdrom sudo dip plugdev users lpadmin
```

Observation:

Confirmed all groups assigned to the current user account.

---

### 4. View User Account Database

Command:

```bash
cat /etc/passwd
```

Result:

Displayed all local user and service accounts stored on the system.

Example Entry:

```text
santicyber:x:1000:1000::/home/santicyber:/bin/bash
```

Observation:

Verified that Linux stores user account information in the `/etc/passwd` file.

---

## Concepts Learned

### User ID (UID)

A unique numerical identifier assigned to each user account.

Example:

```text
1000
```

---

### Group ID (GID)

A numerical identifier assigned to a user's primary group.

Example:

```text
1000
```

---

### Groups

Groups are used to organize users and control permissions on files and directories.

Examples observed:

* adm
* cdrom
* sudo
* dip
* plugdev
* users
* lpadmin

---

### Service Accounts

Linux creates system accounts for services and background processes.

Examples observed:

* root
* daemon
* sys
* cups
* avahi
* systemd-network

These accounts allow services to operate securely without using a normal user account.

---

## What I Learned

* Linux identifies users using usernames and numerical User IDs (UIDs).
* Users belong to one or more groups.
* Groups help control access to files and directories.
* The `id` command displays detailed user and group information.
* The `groups` command lists all group memberships.
* User account information is stored in `/etc/passwd`.
* Linux uses service accounts to run background services securely.

---

## Skills Practiced

* Using `whoami`
* Using `id`
* Using `groups`
* Viewing `/etc/passwd`
* Understanding UIDs and GIDs
* Understanding Linux group memberships
* Identifying service accounts

---

## Conclusion

This lab introduced Linux user and group management concepts. I learned how to identify the current user, view group memberships, locate user account information, and understand the role of service accounts within a Linux operating system.
