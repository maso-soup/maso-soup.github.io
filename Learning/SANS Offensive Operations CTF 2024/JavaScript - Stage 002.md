We heard you like JavaScript? So we scrambled some nice JavaScript ☕ code for you to review! Review the provided code snippet and send appropriate API request to get the flag!

Code:

```javascript
router.post('/api/stages/2', async (req, res) => {
	const { password } = req.body;

	if (password == 0.1 + 0.2) {
		return res.json({ flag: flags[1] });
	}

	return res.status(401).json({ message: 'No flag for you!' });
});
```

Password Condition:

0.1 + 0.2 resolves to 0.30000000000000004

Previous Stage Snippet modified:

```javascript
fetch('http://js.pwn.site:1995/api/stages/2', {
  method: 'POST', // or 'PUT'
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    password: 0.30000000000000004,
  }),
})
.then(response => response.json())
.then(data => console.log(data))
.catch((error) => {
  console.error('Error:', error);
});
```

Flag:

flag{IEEE-754-floating-with-you!}

