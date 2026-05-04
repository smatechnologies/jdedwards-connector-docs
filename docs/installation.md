---
title: Installation
description: "Step-by-step instructions for installing and configuring the JDEdwards Connector on a JDEdwards Enterprise Server."
tags:
  - Procedural
  - System Administrator
  - Installation
---

# Installation

## What is it?

The JDEdwards Connector installation deploys the connector software on a JDEdwards Enterprise Server, configures database connections for report status monitoring, and adds the JDEdwards E1 job subtype to Enterprise Manager.

Complete the installation on every Enterprise Server that requires OpCon to start JDEdwards reports. A single OpCon instance can manage connectors installed on multiple Enterprise Servers.

Use this installation when you need to:

- Connect OpCon to a JDEdwards Enterprise Server for the first time
- Add a new Enterprise Server to an existing OpCon and JDEdwards integration
- Reconfigure the connector after a JDEdwards database connection change

## Before you begin

Verify the following software requirements before starting the installation.

| Component | Requirement |
|---|---|
| OpCon | Release 19.0 or higher |
| Java | Embedded OpenJDK 11 — included in the connector package, no separate installation needed |
| JDEdwards | Enterprise One |
| Database | SQL Server or Oracle |

## Installation steps

### Step 1 — Install the OpCon Windows Agent

The connector requires an OpCon Windows Agent on the same server as the JDEdwards Enterprise Server. Install the Windows Agent before proceeding.

---

### Step 2 — Install the connector

1. Copy `SMAJDEdwardsConnector-win.zip` to the Enterprise Server.
2. Extract the zip file into the installation directory of your choice.

After extraction, the directory contains the following files:

| File or folder | Purpose |
|---|---|
| `SMAJDEdwards.exe` | The connector executable |
| `EncryptValue.exe` | Utility for encrypting passwords before placing them in the config file |
| `Connector.config` | The connector configuration file |
| `java\` | Embedded OpenJDK 11 runtime |
| `emplugins\` | Enterprise Manager job subtype plugin |

---

### Step 3 — Create the XML directory

Create a directory to store the XML files the connector generates when submitting reports.

The directory name must match the `XML_DIRECTORY` value in `Connector.config`. The default is `xml`, which means you create the folder at `installation_dir\xml`.

---

### Step 4 — Create the JDEPath global property

In OpCon, create a global property named `JDEPath` and set its value to the full path of the connector installation directory.

Job definitions reference this property as `[[JDEPath]]` to locate the connector executable. This makes it easy to move the connector without updating every job definition individually.

---

### Step 5 — Install the Enterprise Manager job subtype

1. Copy the plugin file from `installation_dir\emplugins\` to the `dropins` folder inside the Enterprise Manager installation directory.
2. If the `dropins` folder does not exist, create it in the Enterprise Manager root directory.
3. Restart Enterprise Manager. The **JD Edwards E1** job subtype is now available when creating Windows jobs.

:::note
If the **JD Edwards E1** subtype does not appear after restarting, close Enterprise Manager and reopen it using **Run as Administrator**. After the subtype loads, you can run Enterprise Manager without administrator privileges.
:::

---

## Configuration

Configure `Connector.config` in the installation directory before running any jobs.

### Encrypting passwords

All passwords in `Connector.config` must be encrypted before you paste them into the file. Use the `EncryptValue.exe` utility that ships with the connector.

```
EncryptValue.exe -v yourpassword
```

The utility prints the encrypted value. Copy and paste it into the appropriate field in `Connector.config`.

---

### General settings

The `[CONNECTOR]` section controls the connector's directories and execution environment.

:::note Path formatting
Directory values must use double backslashes (`\\`) or forward slashes (`/`) — not single backslashes. Each directory path must end with a trailing separator (`\\` or `/`).
:::

| Setting | What it does | Default |
|---|---|---|
| `NAME` | Connector instance name — do not change this value | `JDEdwards Connector` |
| `JOB_OUTPUT_DIRECTORY` | Directory where the connector places output files | (none) |
| `JDE_OUTPUT_DIRECTORY` | Directory where JDEdwards writes its log files. The connector reads `jde.log` and `jdedebug.log` from here when a report fails | (none) |
| `XML_DIRECTORY` | Directory for the XML files created by the RUNUBEXML utility. The connector appends this to the installation path | `xml` |
| `RUNUBEXML_PATH` | Full path to the directory containing the RUNUBEXML utility | (none) |
| `EXECUTION_HOST` | Name of the host where reports run. Used to look up the correct record in the JDEdwards status table | (none) |
| `DEBUG` | Set to `ON` to enable verbose logging. Set to `OFF` for normal operation | `OFF` |

---

### Database connection settings

The connector queries the JDEdwards database to monitor report status. Define at least one database connection using a `[DATABASE1]` section. Add more connections by incrementing the number: `[DATABASE2]`, `[DATABASE3]`, and so on.

| Setting | What it does |
|---|---|
| `DATABASE_CONNECTION_NAME` | A name you assign to this connection. Job definitions reference this name to select which database to query |
| `DATABASE_TYPE` | The database software. Use `SQL_SERVER` or `ORACLE` |
| `DATABASE_DRIVER` | The JDBC driver class. Use `net.sourceforge.jtds.jdbc.Driver` for SQL Server or `oracle.jdbc.OracleDriver` for Oracle |
| `DATABASE_URL` | The JDBC connection string for the database |
| `DATABASE_SCHEMA` | The schema that contains the JDEdwards status table (`F986110`) |
| `DATABASE_USER` | A database user with read access to the `F986110` table |
| `DATABASE_USER_PASSWORD` | The database user's password, encrypted using `EncryptValue.exe` |

---

### Example configuration

The following example defines one SQL Server connection and one Oracle connection.

```
[CONNECTOR]
NAME=JDEdwards Connector
JOB_OUTPUT_DIRECTORY=
JDE_OUTPUT_DIRECTORY=c:\\JDEdwardsPPack\\E910\\PrintQueue
XML_DIRECTORY=xml
RUNUBEXML_PATH=c:\\JDEdwardsPPack\\E910\\system\\bin32
EXECUTION_HOST=ENTSQL2013
DEBUG=OFF

[DATABASE1]
DATABASE_CONNECTION_NAME=DBSERVER1
DATABASE_TYPE=SQL_SERVER
DATABASE_DRIVER=net.sourceforge.jtds.jdbc.Driver
DATABASE_URL=jdbc:jtds:sqlserver://192.168.239.146/JDETEST
DATABASE_SCHEMA=SVM910
DATABASE_USER=user
DATABASE_USER_PASSWORD=5f0e163eeda2e9672c153c2e4a8d82730e44ee77c3ac568fee9d98c80be73810

[DATABASE2]
DATABASE_CONNECTION_NAME=DBSERVER2
DATABASE_TYPE=ORACLE
DATABASE_DRIVER=oracle.jdbc.OracleDriver
DATABASE_URL=jdbc:oracle:thin:@//192.168.178.30:1521/E910DB
DATABASE_SCHEMA=SVM910
DATABASE_USER=user
DATABASE_USER_PASSWORD=5f0e163eeda2e9672c153c2e4a8d82730e44ee77c3ac568fee9d98c80be73810
```

---

## FAQs

**Can I install the connector on a server that already has a Windows Agent?**
Yes. The connector installs separately from the Windows Agent. Both must be present on the same Enterprise Server.

**Do I need to install Java separately?**
No. The connector package includes an embedded OpenJDK 11 runtime in the `java\` directory. No system-level Java installation is required.

**Can I define multiple database connections in one configuration file?**
Yes. Add additional `[DATABASE(n)]` sections and increment the number for each connection. Each job definition references a connection by its `DATABASE_CONNECTION_NAME` value.

**Why must I use double backslashes in path values?**
Java treats a single backslash as an escape character. Use double backslashes (`\\`) or forward slashes (`/`) for all directory paths. Each path must also end with a trailing separator.

**What if the JD Edwards E1 job subtype does not appear after restarting Enterprise Manager?**
Close Enterprise Manager and reopen it using **Run as Administrator**. The subtype should appear after this step. You can run Enterprise Manager without administrator privileges after the initial load.

---

## Glossary

**Connector.config** — The configuration file for the JDEdwards Connector. Contains general settings, directory paths, and one or more database connection definitions.

**DATABASE_CONNECTION_NAME** — The name you assign to a database connection in `Connector.config`. Job definitions use this name to select which database the connector queries for status.

**EncryptValue.exe** — The password encryption utility included with the connector. Run it before placing any password into `Connector.config`.

**emplugins** — The folder in the connector installation that contains the Enterprise Manager job subtype plugin.

**F986110** — The JDEdwards database table that stores report job status records. The connector queries this table to check whether a submitted report has completed.

**JDEPath** — The OpCon global property that holds the connector installation path. Job definitions reference it as `[[JDEPath]]`.

**RUNUBEXML** — The JDEdwards command-line utility the connector uses to submit reports. Must be present on the Enterprise Server.

**Related topics:**

- [Overview](./overview.md)
- [Operation](./operation.md)
