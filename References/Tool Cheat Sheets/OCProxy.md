## Build OCProxy

You need `ocproxy` from http://github.com/cernekee/ocproxy. You have to build it, to do so on xubuntu you'll have to install the autoconf package with apt. 

`apt install autoconf`

To do so on Kali you will most likely need to install the `libevent-dev` package.

`apt install libevent-dev`

After installing the above prerequisites, pull down the ocproxy repo and build the binary:

```
cd /opt

sudo git clone https://github.com/cernekee/ocproxy.git

cd ocproxy

sudo ./autogen.sh

sudo ./configure

sudo make
```

## Create Initial Proxy Connection Scripts

Use the following to create the XXX-proxy-script.sh file, replacing the -c switch parameter with the URL in the output of the p11tool --list-all-certs command described above.

```

#!/usr/bin/env bash

trap "trap - SIGTERM && kill -- -$$" SIGINT SIGTERM EXIT

exec 3< <(/usr/sbin/openconnect -c 'pkcs11:model=...' XXX.XXX.XXX.XXX -S --script "/opt/ocproxy/ocproxy -D 1090")

grep -m 1 "Connected (script) as" <&3 ; sleep 1s

```

Use the following to create the XXX-proxy-script.sh file, replacing the -c switch parameter with the URL in the output of the p11tool --list-all-certs command described above.

```

#!/usr/bin/env bash

trap "trap - SIGTERM && kill -- -$$" SIGINT SIGTERM EXIT

exec 3< <(/usr/sbin/openconnect -c 'pkcs11:model=...' XXX.XXX.XXX.XXX -S --script "/opt/ocproxy/ocproxy -D 1091")

grep -m 1 "Connected (script) as" <&3 ; sleep 1s

```
