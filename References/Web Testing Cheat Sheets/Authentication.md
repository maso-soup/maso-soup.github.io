### Where to look

- Look closely at login pages and login mechanisms
- Look closely at where identity and trust reside throughout the application
### What to try

- Enumerate for correct usernames by looking for discrepancies in Status codes, Error messages, Subtle differences, Lock outs and Response times. 
- IP protection bypass with `X-Forwarded-For: 127.0.0.1` or others
- IP block bypass with timer reset using valid credentials
- Try JSON array of password string values to bypass rate limiting
- Check MFA bypass after first login, but before code. 
- Attempt Brute forcing MFA code
- Check MFA bypass by checking authentication methodology of second factor, i.e. using a different user cookie with the code request
- Check for easily guessable or insecure password reset URLs or requests
- Check for easily guessable or brute forceable persistent cookies
- Begin legit "forgot password" and check for ways to change target user



### More Details

More details can be found at [[Cyber Security/Learning/Web Security Academy/Authentication|Authentication]]
