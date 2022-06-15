# Zabbix Iostat Monitoring
Zabbix 5.4+ template for monitor iostat output, alerts for high disk utilization and etc
Test enviroment:  
* Debian 11 Bullseye
* iostat 12.5.2
* jq 1.6

# Install
Place userparameter file into zabbix_agentd.d directory

Only 2 external dependencies are needed to run this template:
* **sysstat**
* **jq**

You can install these tools from your system package manager, for example:  
  
Debian/Ubuntu:
`apt install sysstat jq`   
RHEL/CentOS:
`yum install sysstat jq`  

I recommend using the latest sysstat package because old versions of iostat contain some bugs with JSON output  
If your system has sysstat older than 12.5.2, I suggest compiling sysstat from [source code](https://github.com/sysstat/sysstat) (with no extra options to make this template works)
