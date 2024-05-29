### Where to look

- Look for parameters in the URL or POST body being passed variables related to access
- Review page source and `robots.txt` for hidden targets
### What to try

- Parameter-based methods in user controlled location such as `/login/home.jsp?admin=true` or `roleid:2` in a post body
- Add `X-Original-URL` header to include path to unauthorized URLs
- Change HTTP methods between GET and POST
- Horizontal privilege escalation `https://insecure-website.com/myaccount?id=123`
- Indirect Object Reference (IDOR) `https://insecure-website.com/customer_account?customer_number=132355`
- IDOR with static files `https://insecure-website.com/static/12144.txt`
- Try out of order multi step processes
- Edit `Referer` header

### More Details

More details can be found at [[Cyber Security/Learning/Web Security Academy/Access Control|Access Control]]