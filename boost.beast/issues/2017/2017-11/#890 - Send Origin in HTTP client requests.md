# #890 - Send Origin in HTTP client requests [Closed]

> Username: vinniefalco  
> Created at: 2017-11-17 04:05:24 UTC  
> Updated at: 2017-11-18 17:51:56 UTC  
> Closed at: 2017-11-18 17:51:56 UTC  
> Url: https://github.com/boostorg/beast/issues/890  

The Origin field is useful for increased security

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-18 17:51:27 UTC  
> Url: https://github.com/boostorg/beast/issues/890#issuecomment-345458714  

Chrome does not emit Origin, so Beast examples should not either. Typical Chrome request:  
```  
GET /x.png HTTP/1.1  
Host: cloudfront-labs.amazonaws.com  
Connection: keep-alive  
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36  
Accept: image/webp,image/apng,image/*,*/*;q=0.8  
DNT: 1  
Referer: http://www.zerohedge.com/  
Accept-Encoding: gzip, deflate  
Accept-Language: en-US,en;q=0.9  
```
