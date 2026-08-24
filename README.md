# 🗄️ End-to-End-Oracle-Database-Administration
![Oracle Database](https://img.shields.io/badge/Oracle%2019c-F80000?style=for-the-badge&logo=oracle&logoColor=white)
![Oracle Linux](https://img.shields.io/badge/Oracle%20Linux%208-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![VirtualBox](https://img.shields.io/badge/VirtualBox-183A61?style=for-the-badge&logo=virtualbox&logoColor=white)
![SQL](https://img.shields.io/badge/Oracle%20SQL-003B57?style=for-the-badge&logo=sqlite&logoColor=white)
![SQL Developer](https://img.shields.io/badge/SQL%20Developer-4D4D4D?style=for-the-badge&logo=oracle&logoColor=white)
![SSH/MobaXterm](https://img.shields.io/badge/MobaXterm%20(SSH)-231F20?style=for-the-badge&logo=ssh&logoColor=white)
![Terminal](https://img.shields.io/badge/Linux%20Terminal-4EAA25?style=for-the-badge&logo=gnometerminal&logoColor=white)

## 📌 Project Overview
Documents a complete hands-on **Oracle Database Administration workflow** showcasing **Oracle 19c database and Oracle Linux 8 setup inside Oracle VM VertualBox**, **remote MobaXterm(or PUTTY) & SQL Developer integration**, and execution of core DBA daily tasks.

This project covers a practical DBA workflow starting from virtual machine provisioning and Oracle environment verification to Multitenant Database administration, remote SSH access, Oracle networking, SQL Developer connectivity, tablespace management, user provisioning, and datafile capacity expansion.

---

## 📋 Project Overview

The environment used in this project consists of:

```text
Windows Host
│
├── Oracle VM VirtualBox
│   │
│   └── Oracle Linux 8
│       │
│       ├── Oracle Database 19c
│       │   ├── CDB: ORCL
│       │   ├── PDB$SEED
│       │   └── PDB1
│       │
│       └── Oracle Listener
│
├── MobaXterm
│   └── Remote SSH Administration
│
└── Oracle SQL Developer
    └── Remote Database Administration
```

The project focuses on practical DBA operations such as:

- Virtual machine provisioning
- Oracle Linux environment verification
- Oracle user and group configuration
- Oracle 19c installation verification
- Oracle environment variables
- Database startup and shutdown
- Oracle Multitenant Architecture
- CDB and PDB management
- PDB `SAVE STATE`
- Oracle Listener management
- Remote administration through SSH
- Oracle Net configuration
- Oracle SQL Developer connectivity
- Tablespace monitoring
- Application tablespace creation
- Database user provisioning
- Privilege management
- Application object creation
- Datafile resizing
- TEMP and UNDO verification

---

# 🛠️ Technologies & Tools

| Category | Technology / Tool |
|---|---|
| Virtualization | Oracle VM VirtualBox |
| Operating System | Oracle Linux 8 |
| Database | Oracle Database 19c |
| Database Architecture | Oracle Multitenant Architecture |
| Database CLI | SQL*Plus |
| Remote Administration | MobaXterm |
| Database Client | Oracle SQL Developer |
| Shell | Bash |
| Container Database | ORCL |
| Pluggable Database | PDB1 |

---

# 🚀 Project Implementation

## Phase 1: Virtual Infrastructure Provisioning

 Set up the underlying server hardware and operating system required for Oracle 19c.

**Tool Used:** Oracle VM VirtualBox

---

### Step 1.1: Create Oracle Linux Virtual Machine

A new virtual machine was created in Oracle VM VirtualBox using Oracle Linux 8.
The Oracle Linux ISO was attached to the virtual machine for operating system installation.

![Virtual Machine Creation](images/1.orcl.png)

---

### Step 1.2: Configure Unattended Installation

Configured the unattended guest OS setup to automate the Linux installation.
We defined the default user as `kushal` and set the hostname to `OLinux8`.

![Unattended Installation Setup](images/2.orcl.PNG)

---

### Step 1.3: Configure Virtual Storage

Allocated a 20.00 GB dynamically allocated Virtual Hard Disk (VDI) to ensure sufficient space for OS, Oracle binaries, and database files.

![Virtual Storage Allocation](images/3.orcl.png)

---

### Step 1.4: Configure VM Hardware

Finalized the VM specifications, allocating 5112 MB (5 GB) of Base Memory (RAM) and 2 CPU cores to meet Oracle 19c's minimum enterprise requirements.

![Virtual Machine Hardware Configuration](images/4.orcl.png)

---

### Step 1.5: Boot Oracle Linux

Successfully booted into Oracle Linux 8 and reached the login screen for the OS administrator user (`kushal`).

![Oracle Linux First Boot](images/4.2.orcl.png)

---

# Phase 2: Oracle Linux & Oracle User Configuration

After the operating system was available, the Oracle Database environment was prepared.
Prepare the Linux environment by creating necessary Oracle groups, users, and resolving GUI access for the installer.

**Tool Used:** Oracle Linux 8 Terminal

---

### Step 2.1: Create Oracle Groups and Oracle User

Logged in as `root` to create the standard Oracle inventory and database administrator groups, followed by the `oracle` software owner user.
**Commands Executed:**
```bash
groupadd oinstall
groupadd dba
useradd -g oinstall -G dba oracle
passwd oracle


```
The standard Oracle groups were created.
---

![Oracle User and Group Configuration](images/5.2.orcl.png)

---

### Step 2.2: X11 Display Forwarding & Oracle 19c Enterprise Installation
 To securely launch the Oracle Universal Installer (OUI) GUI from the terminal, X11 access control was disabled by the screen owner (`kushal`). After switching to the `oracle` user, exporting the display variable, and launching `./runInstaller`, the following enterprise-level configurations were defined in the GUI to build the infrastructure:

* **Installation Type:** Selected *Create and configure a single instance database* (Server Class, Enterprise Edition).
* **Storage Allocation:** Mapped Oracle Base to `/u01/app/oracle` and Oracle Home to `/u01/app/oracle/product/19c/dbhome_1`.
* **Multitenant Architecture:** Provisioned a Container Database (Global Database Name: **`ORCL`**) and a dedicated Pluggable Database (**`PDB1`**).
* **Memory & Character Set:** Configured Automatic Memory Management (AMM) and set the database character set to `AL32UTF8`.
* **Root Scripts Execution:** Suspended the installer midway to manually execute `orainstRoot.sh` and `root.sh` via the root terminal, finalizing OS-level privileges and inventory registration.

**Commands Executed:**
```bash
whoami  # (Verified user: kushal)
xhost +
su - oracle
export DISPLAY=:0
cd /u01/app/oracle/product/19c/dbhome_1
./runInstaller


```

![Root login , oracle user creation, db installation ](images/5.orcl.png)

---

# Phase 3: Oracle 19c Environment Verification

The Oracle Database installation and environment configuration were verified from Oracle Linux.

**Tool Used:** Oracle Linux Terminal

---

### Step 3.1: Silent Installation Verification (opatch)

Bypassed the GUI and used Oracle's opatch utility to definitively prove that Oracle Database 19c (19.0.0.0.0) was successfully installed in the Oracle Home directory.

```bash
/u01/app/oracle/product/19c/dbhome_1/OPatch/opatch lsinventory
```

![Oracle Environment Variables](images/6.orcl.png)

---

### Step 3.2: Logs, OS Packages, and Auto-Start Check

Verified the installation logs exist, confirmed the Oracle pre-installation RPMs are present (which configure kernel limits), and checked /etc/oratab for instance registration.
Commands Executed:

Bash

```bash
ls -ltr /u01/app/oraInventory/logs/
rpm -qa | grep oracle-database-preinstall
cat /etc/oratab | grep -v "^#"
```


![Oracle 19c Installation Verification](images/7.orcl.png)

---

### Step 3.3: Bash Profile Automation & Process Check

Verified the ~/.bash_profile for the oracle user to ensure $ORACLE_HOME and $ORACLE_SID load automatically. We then checked if the background process Monitor (pmon) was running

```bash
cat ~/.bash_profile
```

The environment variables included Oracle configuration such as:

```bash
export ORACLE_BASE=/u01/app/oracle
export ORACLE_HOME=$ORACLE_BASE/product/19c/dbhome_1
export ORACLE_SID=ORCL
export PATH=$PATH:$ORACLE_HOME/bin
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

The Oracle environment variables were also checked directly.

```bash
echo $ORACLE_SID
echo $ORACLE_HOME
```

The Oracle Process Monitor was checked.

```bash
ps -ef | grep pmon
```
---

![Oracle Bash Profile Configuration](images/8.png)

---

# Phase 4: Database Startup & Core DBA Operations

**Tools Used:**

- Oracle Linux Terminal
- SQL*Plus

---

### Step 4.1: Connect as SYSDBA

Logged into SQL*Plus as SYSDBA, started the database instance, and manually forced the listener registration for immediate connectivity.
Commands Executed:

```bash
sqlplus / as sysdba
startup;
alter system register;
exit
```

---

Start listener
```
lsnrctl start
```

![Database Startup](images/9.png)

---



### Step 4.2: Listener Status Verification

Checked the status of the OS-level listener to ensure it is actively accepting connections for ORCL and pdb1.
Commands Executed:

```sql
lsnrctl status
```



![Database Status Verification](images/10.png)

---

# Phase 5: Oracle Multitenant Architecture

The Oracle Database environment uses Multitenant Architecture.

```text
CDB$ROOT
│
├── PDB$SEED
│
└── PDB1
```

---

### Step 5.1: Verify Oracle Instance



```sql

sqlplus / as sysdba
select instance_name, status, version from v$instance;
select name, db_unique_name, open_mode from v$database;
```

### Step 5.2: Check Available PDBs

The available Pluggable Databases were checked.

```sql
show pdbs;
```

The environment includes:

```text
PDB$SEED
PDB1
```

---

### Step 5.3: Open PDB1

The application Pluggable Database was opened.

```sql
alter pluggable database pdb1 open;
```

---

The state of `PDB1` was saved.

```sql
alter pluggable database pdb1 save state;
```

This allows `PDB1` to automatically return to its saved state after a CDB restart.

The PDB status was verified again.

```sql
show pdbs;
```

![PDB Management and Save State](images/11.png)

---


### Step 5.4: Performed a clean SHUTDOWN IMMEDIATE to prevent data corruption


```bash
shutdown immediate;
```

---


![Oracle Listener Status](images/12.png)

---

# Phase 6: Remote Administration Setup (SSH)

Establish secure remote access to the Linux server to simulate a real-world DBA's remote workflow using MobaXterm(or putty).
Application Used: MobaXterm (Windows)

### Step 6.1: Configuring MobaXterm SSH Session

Created a new SSH session targeting the Oracle Linux VM's IP address (10.89.212.62) specifically for the oracle user.


![Database Shutdown and Restart](images/13.png)

---

# Step 6.2: Remote Health Check & DB Startup

Logged in remotely via SSH, checked server uptime, and started the database to prepare for client connections.
Commands Executed:

---

```bash
whoami
uptime
sqlplus / as sysdba
startup;
select instance_name, status from v$instance;
select name, db_unique_name, open_mode from v$database;
alter system register;
exit
```

![MobaXterm SSH Configuration](images/14.png)

---

###Step 6.3: Network Files Troubleshooting 

Started the listener remotely and checked the tnsnames.ora configuration file to ensure the network parameters (Host, Port 1521, Service Name) were correct for client access.
Commands Executed:

```bash
lsnrctl start
lsnrctl status
cd $ORACLE_HOME/network/admin
ls -l
cat tnsnames.ora

```
![Remote Oracle Administration Through SSH](images/15.png)


---

# Phase 7: Client Configuration (SQL Developer)

Connect a GUI database IDE to the remote Oracle Database for complex SQL administration.
Application Used: Oracle SQL Developer (Windows)

---

### Step 7.1: Configured a new database connection named orcl using the sys user with the SYSDBA role. Pointed it to the remote IP (10.89.212.62), Port 1521, and SID ORCL. The test returned Status: Success


![Oracle Net Configuration](images/16.png)

---

### Step 7.2: Comprehensive Tablespace Usage Report
Executed a SQL Join to generate a professional storage report displaying TOTAL_MB, USED_MB, FREE_MB, and USED_PERCENT. This is critical for predicting when a database will run out of space.
Query Executed:


```sql
select 
    df.tablespace_name, 
    df.file_name,
    round(df.bytes / 1024 / 1024, 2) as total_mb, 
    round((df.bytes - nvl(fs.free_bytes, 0)) / 1024 / 1024, 2) as used_mb, 
    round(nvl(fs.free_bytes, 0) / 1024 / 1024, 2) as free_mb, 
    round(((df.bytes - nvl(fs.free_bytes, 0)) / df.bytes) * 100, 2) as used_percent,
    df.autoextensible
from dba_data_files df
left join 
    (select file_id, sum(bytes) as free_bytes 
     from dba_free_space group by file_id) fs
on df.file_id = fs.file_id
order by used_percent desc, df.tablespace_name;
```
![Oracle SQL Developer Connection](images/17.png)


---

### Step 7.3 :

Switched to pdb1, created a dedicated application tablespace (dba_lab_ts), and provisioned a new application user (dba_lab_user) mapping it to the new tablespace with strict privileges.
Commands Executed:

```sql
alter session set container = pdb1;

create tablespace dba_lab_ts
datafile 'dba_lab_ts01.dbf' size 100m autoextend on next 50m maxsize 500m;

select tablespace_name, status, contents from dba_tablespaces 
where tablespace_name = upper('dba_lab_ts');

create user dba_lab_user identified by labuser123
default tablespace dba_lab_ts
temporary tablespace temp
quota unlimited on dba_lab_ts;

grant create session to dba_lab_user;
grant create table to dba_lab_user;
grant create sequence to dba_lab_user;
```

![Tablespace Usage Report](images/18.png)

---

# Phase 8 : Application User Client Connection

Created a new, dedicated SQL Developer connection specifically for the dba_lab_user, targeting the Service Name pdb1 instead of the CDB SID.

```text
Username: DBA_LAB_USER
Service Name: PDB1
Port: 1521
Host: <Oracle Linux VM IP>
```

![Application Tablespace Creation](images/19.png)

---

### Step 8.1: Application Object Creation

Logged in using the new App_User_pdb1 connection and created a dummy application table (employees) to prove the user has correct privileges and that objects are routing to the correct tablespace.
Commands Executed:

```sql
create table employees (
    emp_id number primary key,
    emp_name varchar2(100),
    salary number
);

select table_name, tablespace_name from user_tables;
```


![Application User Creation and Privileges](images/20.png)


---

### Step 8.3 : Advanced Capacity Planning & System Parameters

Logged back into the SYSDBA session to simulate adding capacity to the growing application. Resized an existing datafile, added a new secondary datafile, and checked system-level parameters for TEMP and UNDO storage.
Commands Executed:

```sql
alter database datafile 'dba_lab_ts01.dbf' resize 200m;

alter tablespace dba_lab_ts 
add datafile 'dba_lab_ts02.dbf' 
size 100m autoextend on next 50m maxsize 500m;

select tablespace_name, file_name, bytes / 1024 / 1024 as size_mb, autoextensible 
from dba_temp_files;

```


The storage architecture becomes:

```text
DBA_LAB_TS
│
├── dba_lab_ts01.dbf
│   └── Resized to 200 MB
│
└── dba_lab_ts02.dbf
    └── Initial Size: 100 MB
```
The TEMP tablespace files were checked.

```sql
select tablespace_name,
       file_name,
       bytes / 1024 / 1024 as size_mb,
       autoextensible
from dba_temp_files;
```

The UNDO configuration was checked using

```sql
show parameter undo;
```

This verifies the database's current UNDO-related configuration


![Application User SQL Developer Connection](images/21.png)

---

# 🧠 Skills Demonstrated

## Oracle Database Administration

- Oracle Database 19c
- SQL*Plus Administration
- SYSDBA Access
- Database Startup and Shutdown
- Instance Monitoring
- Database Status Verification

## Oracle Multitenant Architecture

- Container Database (CDB)
- Pluggable Database (PDB)
- PDB Management
- `PDB$SEED`
- `PDB1`
- PDB `SAVE STATE`
- Container Switching

## Oracle Linux Administration

- Linux User Management
- Linux Group Management
- Oracle Environment Variables
- `.bash_profile`
- Oracle Home
- Oracle SID
- Process Monitoring
- Filesystem and Environment Verification

## Oracle Networking

- Oracle Listener
- Port 1521
- Dynamic Service Registration
- Oracle Net
- `tnsnames.ora`
- Remote Database Connectivity

## Remote Administration

- SSH
- MobaXterm
- Remote Oracle Database Administration
- Remote Listener Management

## Oracle Storage Management

- Tablespaces
- Datafiles
- Free Space Monitoring
- Tablespace Usage Reporting
- AUTOEXTEND
- Datafile Resize
- Add Datafile
- TEMP Tablespace
- UNDO Configuration

## Security & User Management

- Database User Creation
- Default Tablespace Assignment
- Temporary Tablespace Assignment
- Storage Quota
- System Privileges
- Application User Connectivity

---


# 🔐 Security Note

The database credentials used in this project are intended only for a local learning environment.

For a production environment:

- Use strong passwords
- Do not expose database credentials
- Follow the principle of least privilege
- Restrict SYSDBA access
- Use proper credential management
- Follow organizational security policies

---

# 👨‍💻 Author

**Kushal Patel**

Oracle DBA | Oracle Linux | SQL | Database Administration

GitHub: [kushal267](https://github.com/kushal267)
