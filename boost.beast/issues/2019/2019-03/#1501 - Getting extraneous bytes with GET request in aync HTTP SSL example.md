# #1501 - Getting extraneous bytes with GET request in aync HTTP SSL example [Closed]

> Username: Xeverous  
> Created at: 2019-03-05 21:19:52 UTC  
> Updated at: 2019-03-05 22:41:22 UTC  
> Closed at: 2019-03-05 22:41:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1501  

I'm running the https://www.boost.org/doc/libs/1_69_0/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp example against [this API](https://poe.watch/api) and **sometimes** getting extraneous bytes in the returned response body.  
  
Note: I removed loading certificates and used `ctx.set_verify_mode(ssl::verify_none);`.  
  
Boost version: 1.69  
OpenSSL version: 1.1  
Compiler: GCC 8.3.1  
Build flags: `-std=c++17 -O3 -pedantic -Wall -Wextra -c -fmessage-length=0 -ffast-math`  
OS: Windows 10  
  
Example run:  
```  
> .\beast_http_client_async_ssl.exe "api.poe.watch" 443 "/leagues"  
HTTP/1.1 200 OK  
Date: Tue, 05 Mar 2019 21:11:42 GMT  
Server: Apache/2.4.29 (Ubuntu)  
Cache-Control: max-age=60  
Expires: Tue, 05 Mar 2019 21:12:42 GMT  
Access-Control-Allow-Origin: *  
X-Cache: MISS from api.poe.watch  
Transfer-Encoding: chunked  
Content-Type: application/json  
  
1d2  
[{"id":1,"name":"Hardcore","display":"Hardcore","hardcore":true,"upcoming":false,"active":true,"event":false,"challenge":false,"start":"2013-01-23T21:00:00Z","end":null,"duration":{"total":null,"elapsed":192845502,"remaining":null}},{"id":2,"name":"Standard","display":"Standard","hardcore":false,"upcoming":false,"active":true,"event":false,"challenge":false,"start":"2013-01-23T21:00:00Z","end":null,"duration":{"total":null,"elapsed":192845502,"remaining":null}}]  
0  
```  
  
What can be the source of `1d2` and `0`? The sequence is sometimes different, usually few extra bytes at the beginning and at the end. The extra bytes are not always present.  
  
I have also found that the existence of extra bytes comes together with absence of X-Cache HIT and Content-Length.  
  
Example correct run:  
```  
> .\beast_http_client_async_ssl.exe "api.poe.watch" 443 "/leagues"  
HTTP/1.1 200 OK  
Date: Tue, 05 Mar 2019 21:13:56 GMT  
Server: Apache/2.4.29 (Ubuntu)  
Cache-Control: max-age=60  
Expires: Tue, 05 Mar 2019 21:14:48 GMT  
Access-Control-Allow-Origin: *  
Age: 7  
X-Cache: HIT from api.poe.watch  
Content-Length: 466  
Content-Type: application/json  
  
[{"id":1,"name":"Hardcore","display":"Hardcore","hardcore":true,"upcoming":false,"active":true,"event":false,"challenge":false,"start":"2013-01-23T21:00:00Z","end":null,"duration":{"total":null,"elapsed":192845628,"remaining":null}},{"id":2,"name":"Standard","display":"Standard","hardcore":false,"upcoming":false,"active":true,"event":false,"challenge":false,"start":"2013-01-23T21:00:00Z","end":null,"duration":{"total":null,"elapsed":192845628,"remaining":null}}]  
```  
  
  
I run the api against few online REST test services, no errors in any of them. Also tried `curl` and issue is not present there either. I have also asked API's author/owner to test it and there is no error on the owner's side.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-05 21:23:06 UTC  
> Updated at: 2019-03-05 21:24:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1501#issuecomment-469862575  

Well those "extraneous bytes" are part of the chunked encoding as indicated in your response header:  
```  
Transfer-Encoding: chunked  
```  
Chunked encoding of course omits the Content-Length which explains your other observation.  
  
"1d2" is hexadecimal for the number 466, which is the exactly the length of your serialized JSON.  
  
Did you modify the client? Because the example code is not capable of printing the chunked-encoding delimeters (the `"1d2\r\n"` and the `"0\r\n\r\n"`), those are removed during parsing and never land inside the message object.

---

## Comment 2

> Username: Xeverous  
> Created at: 2019-03-05 21:25:40 UTC  
> Updated at: 2019-03-05 21:27:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1501#issuecomment-469863390  

The only modification I have made is disabling SSL verifying.  
  
The same issue appears in the actual project, which instantly fails upon first character in the JSON. The body is retrieved as `res_.body()`.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-03-05 21:34:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1501#issuecomment-469866279  

SSL verification should not have an effect on the interpretation of the body. I am investigating.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-03-05 21:45:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1501#issuecomment-469870060  

Ah yes, this is working as designed! `operator<<` merely serializes the message to the output stream. Because the header specifies the chunked transfer encoding, you will see chunked output. The body of course does not contain this, so if you print `res_.body()` you should see only the JSON. The `operator<<` really is there for debugging, to make it easy to see a message.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-03-05 21:46:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1501#issuecomment-469870455  

If you simply must have `operator<<` print the message without the chunked encoding, you can edit the message first. Call this function before printing the message:  
```  
res_.prepare_payload();  
```  
This will remove the chunked encoding, and insert a Content-Length field with the correct size. Then when you print it with `operator<<` it will not have the chunked output.

---

## Comment 6

> Username: Xeverous  
> Created at: 2019-03-05 22:41:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1501#issuecomment-469888099  

Initially `res_.body()` did not worked for me either, but now I have found the bug in my code:  
  
```cpp  
		std::future<boost::beast::http::response<boost::beast::http::string_body>> request =  
			async_http_get(ioc, ctx, poe_watch_api::target, poe_watch_api::port, "/leagues");  
  
		ioc.run();  
  
		const std::string& result = request.get().body(); // calls .body() on rvalue  
```
