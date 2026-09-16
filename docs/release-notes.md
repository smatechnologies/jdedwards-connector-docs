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

:::note

The connector has received maintenance since 19.1.1, including updates to the embedded Java runtime and to the build and code-signing pipeline. These changes are not individually versioned in this release history. Check with your support contact to confirm the current release for your environment.

:::

## 19

### 19.1.1

**Released:** 2019

This release replaces the logging framework and introduces a new installer format, embedded Java, a new password encoding mechanism, and a renamed configuration file.

#### Migration considerations

- **New installer format.** Files are now extracted from a zip file into the desired installation directory rather than using a traditional installer.
- **Embedded Java included.** The connector now ships with an embedded Java version (OpenJDK 11). There is no longer a dependency on the Java version installed on the host system.
- **New password encoding mechanism.** Use the `EncryptValue.exe` utility to encode passwords for the configuration file, and regenerate any values carried over from an earlier release. The utility encodes rather than encrypts — see [Installation](./installation.md) for what that does and does not protect.
- **Configuration file renamed.** The configuration file has been renamed from `Agent.config` to `Connector.config`.

#### Fixes

- **Replaced log4j with slf4j and logback.** Updated the logging framework to address security and compatibility concerns.
