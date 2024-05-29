Read the following code and send an appropriate request to the API endpoint to get the flag!

```javascript
router.post('/api/stages/1', async (req, res) => {
	const { password } = req.body;

	if (password == "b" + "a" + +"a" + "a") {
		return res.json({ flag: flags[0] });
	}

	return res.status(401).json({ message: 'No flag for you!' });
});
```

This `"b" + "a" + +"a" + "a"` resolves to `baNaNa`. Use javascript and fetch in devtools console to send that string to the API endpoint. 

```javascript
fetch('http://js.pwn.site:1995/api/stages/1', {
  method: 'POST', // or 'PUT'
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    password: 'baNaNa',
  }),
})
.then(response => response.json())
.then(data => console.log(data))
.catch((error) => {
  console.error('Error:', error);
});
```

Flag:

flag: "flag{B-baNaNa?baNaNa!baNaNa!baNaNa!}"