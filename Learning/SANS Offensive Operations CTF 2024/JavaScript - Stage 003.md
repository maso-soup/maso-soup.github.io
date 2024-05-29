We heard you like JavaScript? So we scrambled some nice JavaScript ☕ code for you to review! Review the provided code snippet and send appropriate API request to get the flag!

Code:

```javascript
router.post('/api/stages/3', async (req, res) => {
    const { password } = req.body;

    const secret = ([] + {})[+!![]] +
        (![] + {})[+!![] + [+[]]] +
        (!![] + [])[+[]] +
        ([] + {})[+!![]] +
        (![] + {})[+!![] + [+[]]] +
        (![] + [])[+!![]] +
        (!![] + [])[+[]];

    if (password == secret) {
        return res.json({ flag: flags[2] });
    }

    return res.status(401).json({ message: 'No flag for you!' });
});
```

Password Condition:

`([] + {})[+!![]] + (![] + {})[+!![] + [+[]]] + (!![] + [])[+[]] + ([] + {})[+!![]] + (![] + {})[+!![] + [+[]]] + (![] + [])[+!![]] + (!![] + [])[+[]]` resolves to `octocat`

Previous Stage Snippet modified:

```javascript
fetch('http://js.pwn.site:1995/api/stages/3', {
  method: 'POST', // or 'PUT'
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    password: 'octocat',
  }),
})
.then(response => response.json())
.then(data => console.log(data))
.catch((error) => {
  console.error('Error:', error);
});
```

Flag:

flag{w31rD-j4v45cr1p7-m0m3nt!}