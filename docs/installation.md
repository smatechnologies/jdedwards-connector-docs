# Installation

The JDEdwards Connector installation consists of multiple steps that are required to complete the installation successfully. 
The connector requires that the target JDEdwards Scheduler implements a database accessible by a JDBC driver. 
Current implementation supports SQL Server and Oracle. 
The JDEdwards Connector also requires the installation of a Windows Agent.

## Supported Software Levels
The following software levels are required to implement the JDEdwards Connector.

- OpCon Release 19.0 or higher.
- Embedded Java OpenJDK 11 (part of installation).
- JDEdwards Enterprise One.

## Supported Databases
The following databases are supported.

- SQL Server.
- Oracle.

## Installation
The installation process consists of the following steps:

- OpCon Windows Agent Installation.
- JDEdwards Connector Installation.
- Adding JDEdwards Connector job subtype to Enterprise Manager.
- JDEdwards Connector Configuration.
 
### OpCon Windows Agent Installation
The JDEdwards Connector requires a SMA OpCon Windows Agent. The Agent should be installed on the same system as the JDEdwards Enterprise server.

### JDEdwards Connector Installation
The JDEdwards connector must be installed on the Enterprise Server that will execute the reports alon with a SMA OpCon Windows Agent. 

Copy the supplied install file SMAJDEdwardsConnector-win.zip and extract it into the installation directory.

After the extraction, the root installation directory contains the connector executable (SMAJDEdwards.exe), the encryption software (EncryptValue.exe), the Connector.config file and two directories, java and emplugins. 
The java directory contains the java software required to execute the connector (OpenJDK 11) and the emplugins directory contains the job sub-type plugin for Enterprise Manager.

### Create XML_DIRECTORY
Create the directory where the generated xml files used by the connector will be stored. The directory name matches the configuration value XML_DIRECTORY (default value is xml meaning
that the xml directory should be created off the root installation directory ***installation_dir***\\xml). 

### Create JDEPath Global Property
Create a global property **JDEPath** that contains the full path of the installation directory.

### Job Subtype Installation
Copy the Enterprise Manager plug-in from the ***installation_dir***\\emplugins directory to the dropins directory of the Enterprise Manager installation. 

If the dropins directory does not exist, create the dropins directory off the root directory. 

Restart Enterprise Manager and a new Windows job subtype called Cegid ORLI will be visible.

If not restart Enterprise Manager using 'Run as Administrator'. After this Enterprise Manager can be used normally.

## JDEdwards Connector Configuration
The configuration of the JDEdwards Connector requires setting the required values in the Connector.config file. 

The Connector.config file contains information for the JDEdwards Connector about required directories, the execution host and database connections. 

It is possible to configure multiple database connections, with the first database connection defined under the heading DATABASE1 and subsequent connections 
incrementing the last number (i.e. DATABASE2, DATABASE3). The sample configuration shows the definitions for a SQL Server connection and an Oracle connection.

All password values placed in the configuration and template files must be encrypted using the EncryptValue.exe utility provided with the connector. When executing the tool, a single argument -v is used to provide the value that must be encrypted.

#### EncryptValue Utility
The EncryptValue utility uses standard 64 bit encryption.

Supports a -v argument and displays the encrypted value

On Windows, example on how to encrypt the value "abcdefg":

```
Encrypt.exe -v abcdefg

```

#### Connector.config configuration
Configure the Connector.config file in the installation directory setting the required information.
The Connector.config contains the following values

Property Name                | Value
-----------------------------| -----------
**[GENERAL SETTINGS]**       | header
**Name**                     | The name of the connector. This value should not be changed.
**JOB_OUTPUT_DIRECTORY**     | The name of the directory where created files will be placed. (i.e. c:\\***installation_dir***\\JobOutput\\). NOTE : The backslash character (\\) is a special Java character and if used should be entered twice (\\\\).Alternatively the slash (/) character can be used instead. The directory name must be finished with a trailing \\\\ or /.
**JDE_OUTPUT_DIRECTORY**     | The name of the directory where the JDEdwards created files can be found (i.e. c:\\JDEdwardsPPack\\E910\\system\\bin32). This directory is checked for the output logs .jde.log and .jdedebug.log when a report completes with an error condition. NOTE : The backslash character (\\) is a special Java character and if used should be entered twice (\\\\).Alternatively the slash (/) character can be used instead. The directory name must be finished with a trailing \\\\ or /.
**XML_DIRECTORY**            | The directory where the connector places the XML files created when using the RUNUBEXML utility. The default is xml (the connector appends this to the installation directory ***installation_dir***).
**RUNUBEXML_PATH**	         | The directory where the RUNUBEXML utility can be found.
**EXECUTION_HOST**           | This is the name of the host system where the report is executed. It is used when searching the database for the status of the report (used in conjunction with the unique jobid returned by RUNUBEXML).
**DEBUG**                    | If the Connector supports a debug mode, it can be used to set the connector into DEBUG mode. Value either ON or OFF (default OFF).
**[DATABASE(n)]**            | header : This can be used to define multiple database connections. The value (n) is incremented for each connection starting at 1
**DATABASE_CONNECTION_NAME** | This defines the name of the connection and is used by the connector to determine which database connection to use to get the status of the job. The -dbname argument submitted with the job definitions is matched to this value to determine which connection to use.
**DATABASE_TYPE**            | This is used to define the database software of the target database. Supported values are SQL_SERVER & ORACLE.
**DATABASE_DRIVER**          | The class name of the database driver used to access the database. Supported values are **net.sourceforge.jtds.jdbc.Driver** for SQL Server connections and **oracle.jdbc.OracleDriver** for Oracle connections.
**DATABASE_URL**             | The connection string for the SQL Server or Oracle database.
**DATABASE_SCHEMA**          | The database schema that contains the F986110 table.
**DATABASE_USER**            | The name of a user that has the appropriate privileges to access the F986110 table.
**DATABASE_USER_PASSWORD**   |The password for the user defined in DATABASE_USER encrypted using the EncryptValue.exe utility.

Example configuration file. 

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
