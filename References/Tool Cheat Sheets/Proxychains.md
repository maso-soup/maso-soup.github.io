## Install and Configure Proxychains

First make sure you have proxychains installed: 

`sudo apt install proxychains4`

Next open the default proxychains configuration file at `/etc/proxychains4.conf`, and replace the bottom line with: `socks5 127.0.0.1 1080`

## Create Pivot Proxychains Config Files

Use the following to create the XXX-socks5.conf file.

```

strict_chain

proxy_dns

remote_dns_subnet 224

tcp_read_time_out 15000

tcp_connect_time_out 8000

[ProxyList]

socks5 127.0.0.1 1090

```

Use the following to create the XXX-socks5.conf file.

```

strict_chain

proxy_dns

remote_dns_subnet 224

tcp_read_time_out 15000

tcp_connect_time_out 8000

[ProxyList]

socks5 127.0.0.1 1091

```
## Connecting via Proxies

First run either the XXX-proxy-script.sh or XXX-proxy-script.sh script. You will be prompted to enter your PIV PIN, then your XXX VPN Password. After logging in, this terminal will just "hang", leave it open.

While connected, you will be able to access any resources we have direct access to, for example the XXX.x0.101.XXX, XXX.x0.102.XXX, and XXX.x0.103XXX Nessus scanners at the same time, just like when connecting via openconnect normally.

You can verify that this first step has successfully completed by checking for 127.0.0.1:1090(1) in the output of netstat -antup.

Once it's up, start your SSH dynamic socks proxy over proxychains, specifying the proper conf file: 

`proxychains -f ./XXX-socks5.conf ssh -D 127.0.0.1:1080 XXX@10.40.103.XXX`