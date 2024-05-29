
## Install and Setup

https://github.com/vanhauser-thc/thc-hydra

`sudo apt install hydra`

## Usage



## Troubleshooting

Error code: Compiled without LIBSSH v0.4.x support, module is not available!

https://github.com/vanhauser-thc/thc-hydra/issues/344

```
#Installed libssh:

apt-get install libssh-dev

#Entered into the directory I've installed hydra in: 

cd /thc-hydra

#Cleaned make as sudo: 

sudo make clean

#Run configure: 

./configure

#Run make install: 

sudo make install
```

#cli-tool 