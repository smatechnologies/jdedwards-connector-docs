# Operation

The Enterprise Manager includes job sub-type definitions for the JDEdwards Scheduler. The job sub-type can be accessed by selecting the JDEdwards E1 job subtype from the 
drop down list when the Windows Job Type has been selected. 

It should be remembered that the JDEdwards Connector does not create job definitions in the JDEdwards database, but rather only uses existing job.


## JDEdwards E1 Job definitions
The JDEdwards E1 Definition, defines a single report to be processed by the JDEdwards Scheduler. 

When defining a JDEdwards E1 job, select a Job Type of Windows and then a Job Sub-Type of JD Edwards E1. 

The JD Edwards E1 Definition screen will then appear.

Field | Description
--------- | -----------
**Connector Location**  | Required field and contains the installed location of the JDEdwards Connector (default value [[JDEPath]]). This should not be changed and the location should be defined in the **JDEPath** property. If more than one JDEdwards Connectors are installed a new global property should be defined and the reentry in this field updated.
**Report Name**         | Required field and contains the name of the Report to start. The Report must be defined in the JDEdwards environment. 
**Report Version**      | Required field and contains the defined version of the report to execute. 
**Batch Queue Name**    | Optional field that defines the batch queue that the report will be processed on. If omitted, the value entered in the request.XML by the RUNUBEXML utility will be used.
**Environment**         | Required field and contains the JDEdwards environment where the report will run.
**Database Name**       | Required field and contains the name of a database connection defined in the connector Connector.config file to use when retrieving the status of the report processing.
**User Name**           | Required field and contains the name of a JDEdwards user that has the required privileges to run the report.
**User Role**           | Required field and contains the role associated with the defined JDEdwards user.
**User Password**
In general the successful completion code for a job is 0. However, for older systems it is possible that this value maybe defined as another integer. This value would be defined in the Agent.configuration (see ***User Defined RC*** section).

## Job Finished processing 

A JDEdwards Scheduled JOB has the following possible return codes:

0	FINISHED_OK, the job completed processing.
1	ERRORED, an exception occurred during job processing.

This means that to check for a successful completion, the Failure Criteria should be set to NE (Not Equal) to 0.

## Logging

The default logging implemented by the connector consists of a maximum cycle of five log files. The log files contain information about the JDEdwards Connector and any jobs run by the JDEdwards Connector. The log files (jdedwardse1.log - jdedwardse1.log.5) are located in the ***installation_dir***\log directory. Information is appended into the log files and any error messages, return codes can be viewed in these log files.

All information produced by the OpCon job is available in the job output and can be retrieved using the OpCon JORS capability. When the job fails, the jde and jdedebug logs are appended to the OpCon job output and can be retrieved using JORS. When used in conjunction with the Event Notification System, the jde error logs can be attached to emails that can be sent to define email or group email addresses.


```

2014-09-25 09:37:43,342 INFO  (JDEdwardsConnector) -------------------------------------------------------------
2014-09-25 09:37:43,389 INFO  (JDEdwardsConnector) SMA JDEwards Connector : 5.20.01.10
2014-09-25 09:37:43,389 INFO  (JDEdwardsConnector) XML files directory    : C:\jdeconn\xml
2014-09-25 09:37:43,389 INFO  (JDEdwardsConnector) Job Output Directory   : null
2014-09-25 09:37:43,389 INFO  (JDEdwardsConnector) Runubexml location     : c:\JDEdwardsPPack\E910\system\bin32
2014-09-25 09:37:43,389 INFO  (JDEdwardsConnector) -------------------------------------------------------------
2014-09-25 09:37:43,389 INFO  (JDEdwardsJobExecutorImpl) Report Name                : R0006P
2014-09-25 09:37:43,389 INFO  (JDEdwardsJobExecutorImpl) Report Version             : XJDE0001
2014-09-25 09:37:43,389 INFO  (JDEdwardsJobExecutorImpl) Environment                : DV910
2014-09-25 09:37:43,389 INFO  (JDEdwardsJobExecutorImpl) User                       : JDE
2014-09-25 09:37:43,389 INFO  (JDEdwardsJobExecutorImpl) User Role                  : *ALL
2014-09-25 09:37:43,389 INFO  (JDEdwardsJobExecutorImpl) -------------------------------------------------------
2014-09-25 09:37:43,654 INFO  (JDEdwardsJobExecutorImpl) Created job base XML file 'C:\jdeconn\xml\R0006P_XJDE0001_base.xml'
2014-09-25 09:37:43,654 INFO  (JDEdwardsConnectionFactory) buildBatchRequestXml command line '"c:\JDEdwardsPPack\E910\system\bin32\runubexml.exe" S "C:\jdeconn\xml\R0006P_XJDE0001_base.xml" "C:\jdeconn\xml\R0006P_XJDE0001_request.xml"'
2014-09-25 09:38:07,023 INFO  (JDEdwardsConnectionFactory) submitJob command line '"c:\JDEdwardsPPack\E910\system\bin32\runubexml.exe" S "C:\jdeconn\xml\R0006P_XJDE0001_request.xml" "C:\jdeconn\xml\R0006P_XJDE0001_result.xml"'
2014-09-25 09:38:08,521 INFO  (JDEdwardsJobExecutorImpl) Job ID '89' returned
2014-09-25 09:38:08,521 INFO  (JDEdwardsConnectionFactory) Connecting to database type (ORACLE) using URL 'jdbc:oracle:thin:@//192.168.168.210:1521/E910DB'
2014-09-25 09:38:08,817 INFO  (JDEdwardsJobExecutorImpl) Connected to database 'jdbc:oracle:thin:@//192.168.168.210:1521/E910DB'
2014-09-25 09:38:08,817 INFO  (JDEdwardsJobExecutorImpl) Job Status Change : JD Edwards Status (Queued)
2014-09-25 09:38:13,918 INFO  (JDEdwardsConnectionFactory) SELECT JCJOBSTS FROM SVM910.F986110 WHERE JCJOBNBR = 89 AND JCEXEHOST = 'ENTSQL2013'
2014-09-25 09:38:14,417 INFO  (JDEdwardsJobExecutorImpl) Job Status Change : JD Edwards Status (Processing)
2014-09-25 09:38:24,433 INFO  (JDEdwardsConnectionFactory) SELECT JCJOBSTS FROM SVM910.F986110 WHERE JCJOBNBR = 89 AND JCEXEHOST = 'ENTSQL2013'
2014-09-25 09:38:24,433 INFO  (JDEdwardsJobExecutorImpl) Job Fin : JD Edwards Status (Done)


```