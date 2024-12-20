# Zabbix Iostat Monitoring Template

This template for Zabbix 6.0.35 is designed to monitor `iostat` output and provide alerts for high disk utilization and other related metrics.

## Test Environment

The template has been tested in the following environment:

- **Operating System**: Debian 11 Bullsey
- **iostat Version**: 12.5.2
- **jq Version**: 1.6

---

## Installation Instructions

### Step 1: Place the User Parameter File

Copy the provided `userparameter` file into the `zabbix_agentd.d` directory on the target system.

### Step 2: Install Dependencies

This template requires two external dependencies to function:

1. **sysstat**
2. **jq**

Install these tools using your system's package manager. Examples:

#### Debian/Ubuntu

```bash
apt install sysstat jq
```

#### RHEL/CentOS

```bash
yum install sysstat jq
```

---

## Recommendations for sysstat Version

We recommend using the latest version of `sysstat` because older versions of `iostat` may have bugs in their JSON output. If your system has a version of `sysstat` older than 12.5.2, consider compiling it from the source:

- [sysstat GitHub Repository](https://github.com/sysstat/sysstat)

To ensure compatibility with this template, compile `sysstat` with default options.