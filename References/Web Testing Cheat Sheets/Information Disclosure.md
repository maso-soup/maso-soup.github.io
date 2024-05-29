### Where to look

- Review error pages closely
- Review page source closely
- Review hidden directories or files via `robots.txt`, `sitemap.xml`, or forced browsing
### What to try

- Fuzz parameters with intention of causing errors and unexpected output, grepping for `error`, `invalid`, `SELECT`, `SQL`, and so on
- Use Burp tools Search, Find comments, Find Scripts, and Discover content
- IDOR allowing information disclosure `GET /user/personal-info?user=carlos`
- Use TRACE to determine possible custom headers in use such as     `X-Custom-IP-Authorization: 127.0.0.1`
- Check for version control history at `/.git`

### More Details

More details can be found at [[Cyber Security/Learning/Web Security Academy/Information Disclosure|Information Disclosure]]