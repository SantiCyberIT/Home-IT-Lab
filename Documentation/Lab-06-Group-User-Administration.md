# Lab 06 - User and Group Administration

## Objective

Simulate onboarding a new IT Support employee by creating a user account, creating a department group, assigning the user to the group, and configuring a shared folder with appropriate permissions.

---

## Environment

* Ubuntu Linux
* VirtualBox
* User Account: santicyber

---

## Scenario

A new employee named **testuser** is joining the IT Support department.

As the administrator, I was responsible for:

* Creating the employee account
* Creating the IT Support group
* Adding the employee to the group
* Creating a shared department folder
* Assigning ownership and permissions
* Verifying access configuration

---

## Tasks Performed

### 1. Created a New User Account

Command:

```bash
sudo adduser testuser
```

Actions Performed:

* Created user account
* Created primary group
* Created home directory
* Assigned password

Verification:

```bash
id testuser
```

Result:

```text
uid=1001(testuser)
gid=1001(testuser)
groups=1001(testuser),100(users)
```

---

### 2. Created IT Support Group

Command:

```bash
sudo groupadd itsupport
```

Verification:

```bash
getent group itsupport
```

Result:

```text
itsupport:x:1002:
```

Observation:

The group was successfully created and assigned a Group ID (GID).

---

### 3. Added User to IT Support Group

Command:

```bash
sudo usermod -aG itsupport testuser
```

Verification:

```bash
groups testuser
```

Result:

```text
testuser : testuser users itsupport
```

Additional Verification:

```bash
getent group itsupport
```

Result:

```text
itsupport:x:1002:testuser
```

Observation:

Verified membership from both the user perspective and the group perspective.

---

### 4. Created Shared Department Folder

Command:

```bash
mkdir itsupport-shared
```

Verification:

```bash
ls
```

Result:

```text
itsupport-shared
```

Observation:

Created a folder to simulate a shared departmental resource.

---

### 5. Assigned Group Ownership

Command:

```bash
sudo chgrp itsupport itsupport-shared
```

Verification:

```bash
ls -ld itsupport-shared
```

Result:

```text
drwxrwxr-x santicyber itsupport itsupport-shared
```

Observation:

The folder was successfully assigned to the IT Support group.

---

### 6. Configured Folder Permissions

Command:

```bash
sudo chmod 770 itsupport-shared
```

Verification:

```bash
ls -ld itsupport-shared
```

Result:

```text
drwxrwx--- santicyber itsupport itsupport-shared
```

Permission Breakdown:

```text
770
```

Owner:

```text
7 = rwx
```

Group:

```text
7 = rwx
```

Others:

```text
0 = ---
```

Observation:

Only the owner and members of the IT Support group have access to the folder.

---

## Concepts Learned

### User Accounts

Linux users are identified by:

* Username
* User ID (UID)

Example:

```text
testuser
```

---

### Groups

Groups are used to manage permissions for multiple users.

Example:

```text
itsupport
```

---

### Group Membership

Users can belong to multiple groups.

Example:

```text
testuser : testuser users itsupport
```

---

### Group Ownership

Folders and files can be assigned to a group.

Example:

```bash
sudo chgrp itsupport itsupport-shared
```

---

### Permissions

Linux permissions are divided into:

* Owner
* Group
* Others

Example:

```text
770
```

Provides:

* Full access to owner
* Full access to group
* No access to others

---

## Skills Practiced

* Creating Linux users
* Creating Linux groups
* Modifying group memberships
* Verifying user accounts
* Verifying group memberships
* Creating shared folders
* Assigning group ownership
* Managing Linux permissions
* Basic access control administration

---

## Results

Successfully simulated the onboarding of a new IT Support employee by:

* Creating a user account
* Creating a department group
* Assigning the user to the department
* Creating a shared folder
* Configuring group ownership
* Applying secure permissions

This lab demonstrates foundational Linux administration skills commonly used in IT Support, Desktop Support, and Junior System Administration roles.

---

## Screenshots

See:

```text
Screenshots/Lab-06/
```
