## Scanning Through Proxies or Port Forwards
When scanning through proxies there are restrictions including the inability to execute SYN or UDP scans apply. An example scan that could be used in conjunction with a proxy would be: 

`nmap -Pn -T4 -sT -p 139 --max-retries 0 XXX.XXX.0.0/16 -oA output`

The `-Pn` flag prevents it from using pings, the 

#cli-tool #nmap 

```
#!/usr/bin/env bash

echo "Running quick Nmap scan to detect open ports."
nmap -Pn -T4 --max-retries 0 -p- -oN nmap.quick $1
echo " "

echo "Running deeper Nmap scan on detected open ports."
nmap -sV -A -p $(grep open nmap.quick | grep -v Warning | sed 's/ open  //g'| awk -F '/' '{print $1}'|tr '\n' ',') -oN nmap.full $1
echo " "

echo "See below for a quick reference list of open ports and detected banners."
grep open nmap.full | grep -v Warning | sed 's/ open  //g'
```