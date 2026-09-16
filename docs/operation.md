---
title: Operation
description: "How to define and run JDEdwards E1 jobs in OpCon, including job definition fields, return codes, and log file locations."
tags:
  - Procedural
  - Reference
  - Automation Engineer
  - Operations Staff
  - Jobs
---

# Operation

## What is it?

The JDEdwards Connector runs JDEdwards reports through OpCon by using the JDEdwards E1 job subtype within a Windows job. When OpCon schedules the job, the connector submits the defined report to the JDEdwards scheduler and monitors its completion status.

Use this documentation when you need to:

- Define a JDEdwards E1 job in Enterprise Manager
- Understand the fields required to submit a report through the connector
- Interpret return codes and log output for a JDEdwards job

## Defining a JDEdwards E1 job

The JDEdwards E1 job subtype is accessed through the Windows job type in Enterprise Manager. It defines a single report to be processed by the JDEdwards scheduler. The connector does not create job definitions in JDEdwards — the report must already exist in the JDEdwards environment.

To define a JDEdwards E1 job, complete the following steps:

1. In Enterprise Manager, open the Job Master for the target schedule.
2. Select the **Add** button to create a new job.
3. In the **Job Type** list, select **Windows**.
4. In the **Job Sub-Type** list, select **JD Edwards E1**. The JD Edwards E1 Definition screen is displayed.
5. Complete the required fields as described in the table below.
6. Select the **Save** button. The job is added to the schedule.

### JDEdwards E1 definition fields

| Field | Required | Description |
|---|---|---|
| **Connector Location** | Yes | The installed location of the JDEdwards Connector. The default value is `[[JDEPath]]`. Do not change this unless more than one connector is installed — in that case, define a new global property and update this field accordingly. |
| **Report Name** | Yes | The name of the report to start. The report must already exist in the JDEdwards environment. |
| **Report Version** | Yes | The version of the report to run. |
| **Batch Queue Name** | Yes | The batch queue on which the report will be processed. Enterprise Manager shows this field as optional, but the connector requires it — see the caution below. |
| **Environment** | Yes | The JDEdwards environment where the report will run. |
| **Database Name** | Yes | The name of a database connection defined in `Connector.config`. Used to retrieve the report status. |
| **User Name** | Yes | A JDEdwards user with the required privileges to run the report. |
| **User Role** | Yes | The role associated with the defined JDEdwards user. |
| **User Password** | Yes | The password for the JDEdwards user. |

## Return codes

A JDEdwards E1 job returns one of the following codes when it completes:

| Code | Status | Description |
|---|---|---|
| `0` | `FINISHED_OK` | The report completed successfully. |
| `1` | `ERRORED` | An exception occurred during job processing, or a required job definition field was left blank. |
| `99` | Configuration error | The `DATABASE_USER_PASSWORD` setting is missing from the database section in `Connector.config`. |
| `401` | Configuration error | The `DATABASE_USER_PASSWORD` setting is present in `Connector.config` but empty. |

:::caution

Complete the **Batch Queue Name** field even though Enterprise Manager marks it optional. If you leave it blank, the plug-in omits the batch queue from the connector's command line, the connector rejects the command line as incomplete, and the job fails with a usage message and return code `1` before the report is submitted.

:::

Set the **Failure Criteria** for the job to **NE** (Not Equal) **0** to treat any non-zero return code as a failure.

:::note

The connector waits for the report to reach a completed or errored state in JDEdwards. It does not time out. A report that is held, queued, or otherwise not progressing leaves the OpCon job running until the report completes or you cancel the OpCon job.

A job that runs far longer than the report should take is usually a configuration problem rather than a slow report. The most common cause is an `EXECUTION_HOST` or `DATABASE_SCHEMA` value in `Connector.config` that does not match the JDEdwards status table, which means the connector never finds the report's status record and keeps checking indefinitely. Check the connector log for a repeating error.

:::

## Logging

The connector writes activity and job status information to log files in `installation_dir\log`.

The active log file is `jdedwardse1.log`. Completed files are rolled into a subdirectory named for the month, with the date and an index in the file name — for example `log\2026-09\jdedwardse1_2026-09-16.0.log`. A new file starts each day, and the index increments when a file reaches 100 MB.

:::caution

Rolled log files are retained indefinitely. The `log` directory grows until you remove old files, so include it in whatever disk monitoring you apply to the Enterprise Server.

:::

All output produced during a job run is available in the OpCon job output and can be retrieved using the Job Output Retrieval System (JORS).

When a job fails, the connector appends the report’s JDEdwards diagnostic files to the OpCon job output. These are held in the `JDE_OUTPUT_DIRECTORY` directory and are named for the report’s own output file, ending in `.jde.log` and `.jdedebug.log` — so they are per-report files rather than a single shared log. You can retrieve the combined output through JORS and use it with the Event Notification System to send the log output as an email attachment to a defined address or distribution list.

### Sample log output

The following example shows the sequence of operations for a successful report run. It was captured from an earlier release, so the timestamp format and the version line differ from current output — the sequence of steps, and the five-second and ten-second status check intervals it shows, are still accurate.

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
2014-09-25 09:38:08,521 INFO  (JDEdwardsConnectionFactory) Connecting to database type (ORACLE) using URL 'jdbc:oracle:thin:@//<oracle-host>:1521/<service-name>'
2014-09-25 09:38:08,817 INFO  (JDEdwardsJobExecutorImpl) Connected to database 'jdbc:oracle:thin:@//<oracle-host>:1521/<service-name>'
2014-09-25 09:38:08,817 INFO  (JDEdwardsJobExecutorImpl) Job Status Change : JD Edwards Status (Queued)
2014-09-25 09:38:13,918 INFO  (JDEdwardsConnectionFactory) SELECT JCJOBSTS FROM SVM910.F986110 WHERE JCJOBNBR = 89 AND JCEXEHOST = '<enterprise-server-host>'
2014-09-25 09:38:14,417 INFO  (JDEdwardsJobExecutorImpl) Job Status Change : JD Edwards Status (Processing)
2014-09-25 09:38:24,433 INFO  (JDEdwardsConnectionFactory) SELECT JCJOBSTS FROM SVM910.F986110 WHERE JCJOBNBR = 89 AND JCEXEHOST = '<enterprise-server-host>'
2014-09-25 09:38:24,433 INFO  (JDEdwardsJobExecutorImpl) Job Fin : JD Edwards Status (Done)
```

## FAQs

**Does the connector create or modify job definitions in JDEdwards?**
No. The connector only submits and monitors existing JDEdwards jobs. You must define the report in JDEdwards before scheduling it through OpCon.

**What should I set for Failure Criteria?**
Set **Failure Criteria** to **NE** (Not Equal) **0**. A return code of `0` indicates successful completion; any other value indicates an error.

**Where do I find log files when a job fails?**
The active log file is `installation_dir\log\jdedwardse1.log`, and older files are in month-named subdirectories beneath it. When a job fails, the connector also appends the report's `.jde.log` and `.jdedebug.log` files to the OpCon job output, which you can retrieve using JORS.

**What does the Batch Queue Name field do?**
It sets the JDEdwards batch queue the report is processed on. Enterprise Manager marks the field optional, but you must complete it: leaving it blank makes the job fail with a usage message before the report is submitted.

**Can I run the same report in multiple JDEdwards environments?**
Yes. Define a separate job for each environment and set the **Environment** and **Database Name** fields accordingly for each job.

## Glossary

**Batch Queue Name** — The optional JDEdwards batch queue where the report will be processed. If not specified, RUNUBEXML assigns the default queue.

**Database Name** — The `DATABASE_CONNECTION_NAME` value from `Connector.config` that identifies which database connection the connector uses to monitor the report status.

**Environment** — The JDEdwards environment (such as a production or development environment) where the report will run.

**ERRORED** — The return code status indicating that an exception occurred during connector processing. Corresponds to return code `1`.

**FINISHED_OK** — The return code status indicating that the report completed successfully. Corresponds to return code `0`.

**JORS** — Job Output Retrieval System. The OpCon capability for retrieving job output, including connector logs and JDEdwards error logs appended on failure.

**User Role** — The JDEdwards role associated with the user who submits the report. Required by JDEdwards to determine the user's access and privileges when running the report.

**Related topics:**

- [Overview](./overview.md)
- [Installation](./installation.md)
