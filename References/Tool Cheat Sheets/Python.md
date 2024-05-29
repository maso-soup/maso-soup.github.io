## Multi-threading



## HTTP Server

[`http.server`](https://docs.python.org/3/library/http.server.html#module-http.server "http.server: HTTP server and request handlers.") can also be invoked directly using the [`-m`](https://docs.python.org/3/using/cmdline.html#cmdoption-m) switch of the interpreter. Similar to the previous example, this serves files relative to the current directory:

`python -m http.server`

The server listens to port 8000 by default. The default can be overridden by passing the desired port number as an argument:

`python -m http.server 9000`

By default, the server uses the current directory. The option `-d/--directory` specifies a directory to which it should serve the files. For example, the following command uses a specific directory:

`python -m http.server --directory /tmp/`

#cli-tool 

## References

HTTP Server: https://docs.python.org/3/library/http.server.html
CLI Template: https://github.com/AnthonyBloomer/python-cli-template

#python

