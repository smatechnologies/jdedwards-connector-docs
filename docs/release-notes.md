---
sidebar_label: 'Release notes'
title: JDEdwards Connector release notes
description: "Version history and change details for the JDEdwards Connector, including new features, migration considerations, and fixes."
tags:
  - Reference
  - System Administrator
  - Automation Engineer
  - Getting Started
---

# JDEdwards Connector release notes

## 19

### 19.1.1

**Released:** 2019

This release replaces the logging framework and introduces a new installer format, embedded Java, updated encryption, and a renamed configuration file.

### Migration considerations

- **New installer format.** Files are now extracted from a zip file into the desired installation directory rather than using a traditional installer.
- **Embedded Java included.** The connector now ships with an embedded Java version (OpenJDK 11). There is no longer a dependency on the Java version installed on the host system.
- **New encryption mechanism.** The connector implements new encryption capabilities. Use the `EncryptValue.exe` utility to encrypt passwords for the configuration file. See [Installation](./installation.md) for details.
- **Configuration file renamed.** The configuration file has been renamed from `Agent.config` to `Connector.config`.

### Fixes

- **Replaced log4j with slf4j and logback.** Updated the logging framework to address security and compatibility concerns.
