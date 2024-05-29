### Where to look

- Look for parameters in the URL or POST body that may pass values to an SQL query or be stored in a database. 

### What to try

- Cheatsheet at  [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
- Determine number of columns in parameter `category=3'+UNION+SELECT+NULL--`, iterating with adding `NULL` while observing output
- For Oracle `' UNION SELECT NULL FROM DUAL--`
- Finding useful data types (strings) `' UNION SELECT 'a',NULL,NULL,NULL-- ` by rotating the string value through

### More Details

More details can be found at [[Cyber Security/Learning/Web Security Academy/SQL Injection|SQL Injection]]