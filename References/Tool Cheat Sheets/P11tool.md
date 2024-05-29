

Use the following commands to retrieve the required URL from the PIV certificate.

```

# Run this command, then copy the URL of the first Object 0 cert

p11tool --list-tokens

# Copy the url displayed in the output of this command into the script

p11tool --list-all-certs 'pksc11:model=INSTERT TOKEN URL THING HERE'

```

`p11tool --list-tokens`

`p11tool --list-all-certs <insert token URL from above command>`

Use the URL of the Object with Label "Certificate for PIV Authentication". Should look like

`pkcs11:model=...`

#p11tool #cli-tool #token #piv