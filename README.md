# Zabbix Iostat/sysstat Monitoring Template

This template for Zabbix 6.0.36 is designed to monitor `iostat` output and provide alerts for high disk utilization and other related metrics.

## Test Environment

The template has been tested in the following environment:

- **Operating System**: Debian 11 Bullseye or CentOS 9 Stream
- **Zabbix Agent**: zabbix-agent2 (Zabbix) 6.0.36
- **iostat/sysstat Version**: 12.5.4
- **jq Version**: 1.6

---

## Installation Instructions

### Step 1: Place the User Parameter File

Copy the provided `userparameter_iostat.conf` file into the `zabbix_agentd.d` directory on the target system.

```text
### UserParameter for disk discovery ###
UserParameter=disk.iostat.discovery,iostat -d | awk 'BEGIN {check=0;count=0;array[0]=0;} {if(check==1 && $1 != ""){array[count]=$1;count=count+1;}if($1~"^Device"){check=1;}} END {printf("{\n\t\"data\":[\n");for(i=0;i<count;++i){printf("\t\t{\n\t\t\t\"{#HARDDISK}\":\"%s\"}", array[i]); if(i+1<count){printf(",\n");}} printf("]}\n");}'

### UserParameter for disk statistics ###
UserParameter=disk.iostat.getstats[*],iostat -xm -o JSON 1 1 | jq -r ".sysstat.hosts[0].statistics[0].disk | .[] | select(.disk_device == \"$1\")"
```

### Step 2: Install Dependencies

This template requires two external dependencies:

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

### Step 3: Restart Zabbix Agent

After adding the UserParameter configuration, restart the Zabbix Agent 2 service:

```bash
sudo systemctl restart zabbix-agent2
```

---

## Testing the Configuration

### Step 1: Test `disk.iostat.discovery`

Run the following command on the Zabbix Agent host:

```bash
zabbix_agent2 -t disk.iostat.discovery
```

Expected output (example):

```json
{"data":[
    {"{#HARDDISK}":"sda"},
    {"{#HARDDISK}":"sdb"}
]}
```

### Step 2: Test `disk.iostat.getstats[*]`

Run the following command, replacing `sda` with a disk name:

```bash
zabbix_agent2 -t disk.iostat.getstats[sda]
```

Expected output (example):

```json
{
    "disk_device": "sda",
    "rrqm/s": 0.12,
    "wrqm/s": 0.34,
    "r/s": 1.56,
    "w/s": 0.78,
    "rMB/s": 0.05,
    "wMB/s": 0.02,
    "avgrq-sz": 128.3,
    "avgqu-sz": 0.02,
    "await": 1.56,
    "r_await": 1.23,
    "w_await": 2.34,
    "svctm": 0.67,
    "%util": 0.45
}
```

### Step 3: Debugging Issues

#### Check Zabbix Agent Logs

```bash
sudo tail -f /var/log/zabbix/zabbix_agent2.log
```

#### Manually Run Commands

**Discovery Command:**

```bash
iostat -d | awk 'BEGIN {check=0;count=0;array[0]=0;} {if(check==1 && $1 != ""){array[count]=$1;count=count+1;}if($1~"^Device"){check=1;}} END {printf("{\n\t\"data\":[\n");for(i=0;i<count;++i){printf("\t\t{\n\t\t\t\"{#HARDDISK}\":\"%s\"}", array[i]); if(i+1<count){printf(",\n");}} printf("]}\n");}'
```

**Get Disk Stats Command (Replace `sda` with a disk name):**

```bash
iostat -xm -o JSON 1 1 | jq -r ".sysstat.hosts[0].statistics[0].disk | .[] | select(.disk_device == \"sda\")"
```

---

## Recommendations for sysstat Version

We recommend using the latest version of `sysstat` because older versions of `iostat` may have bugs in their JSON output. If your system has a version of `sysstat` older than 12.5.4, consider compiling it from the source:

- [sysstat GitHub Repository](https://github.com/sysstat/sysstat)

To ensure compatibility with this template, compile `sysstat` with default options.
