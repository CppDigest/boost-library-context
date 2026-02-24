# #592 - handling of http code 422 [Closed]

> Username: jimhansson  
> Created at: 2017-07-04 06:43:31 UTC  
> Updated at: 2019-06-25 16:21:19 UTC  
> Closed at: 2017-07-04 15:14:52 UTC  
> Url: https://github.com/boostorg/beast/issues/592  

using version 66 of beast I get a bad_status when the server return http status 422, not sure what I really expect instead but something more would have been nice. The body of response may still be of value, but as I understand it is not possible to restart the parse, and get the rest of the information?.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-04 06:47:16 UTC  
> Updated at: 2017-07-04 06:47:51 UTC  
> Url: https://github.com/boostorg/beast/issues/592#issuecomment-312794535  

You are reading an HTTP response with a 422 status-code? You should get the whole message not an error. Can you show the exact serialized response that you are getting?

---

## Comment 2

> Username: jimhansson  
> Created at: 2017-07-04 13:22:55 UTC  
> Url: https://github.com/boostorg/beast/issues/592#issuecomment-312876863  

I have modified the first example http://vinniefalco.github.io/beast/beast/quick_start.html#beast.quick_start.http_client so that if the check of `ec` after the `read` operation fails, I try to print the `res` to standard output. All i get is `HTTP/1.1 w65 <unknown-status>` while if the operation completes without bad_status i am able to print the whole body.  
  
another question i have is how do i get the exact http status code?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-04 13:47:18 UTC  
> Updated at: 2017-07-04 13:48:20 UTC  
> Url: https://github.com/boostorg/beast/issues/592#issuecomment-312882589  

We need to see what the server is actually sending back, try just reading from the socket normally, into a buffer (use `socket.async_read_some` in a loop) and print the buffers. Or use a packet inspection tool like wireshark.  
  
>another question i have is how do i get the exact http status code?  
Call `result_int()` http://vinniefalco.github.io/beast/beast/ref/beast__http__header/result_int.html

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-07-04 15:09:31 UTC  
> Url: https://github.com/boostorg/beast/issues/592#issuecomment-312900894  

Something like this might work (note: untested)  
```  
    // Read the response  
    //http::read(sock, b, res, ec); // comment this out  
    std::string s;  
    s.resize(4096);  
    s.resize(sock.read_some(boost::asio::const_buffer(buf.data(), buf.size()), ec));  
    // At this point, look at the value of `s`  
```

---

## Comment 5

> Username: jimhansson  
> Created at: 2017-07-04 15:09:38 UTC  
> Url: https://github.com/boostorg/beast/issues/592#issuecomment-312900910  

HTTP/1.1 422  
Date: Tue, 04 Jul 2017 06:49:04 GMT  
Content-Type: application/json  
Transfer-Encoding: chunked  
Connection: keep-alive  
Set-Cookie: __cfduid=d3a7f17d68e21ba854be2bf736c73ce261499150944; expires=Wed, 04-Jul-18 06:49:04 GMT; path=/; domain=.poloniex.com; HttpOnly  
Cache-Control: private  
Server: cloudflare-nginx  
CF-RAY: 3790247bfaa70761-AMS  
  
55  
{"error":"Nonce must be greater than 999999999999999. You provided 999999999999999."}

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-04 15:10:49 UTC  
> Url: https://github.com/boostorg/beast/issues/592#issuecomment-312901156  

LOL polo :)  
  
I don't think `HTTP/1.1 422\r\n` is a valid status-line, its missing the reason-phrase. Let me look at rfc7230.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-07-04 15:12:21 UTC  
> Url: https://github.com/boostorg/beast/issues/592#issuecomment-312901456  

BNF (https://tools.ietf.org/html/rfc7230#section-3.1.2)  
```  
    status-line = HTTP-version SP status-code SP reason-phrase CRLF  
    reason-phrase = *( HTAB / SP / VCHAR / obs-text )  
```  
  
There needs to be at least a space after the status-code, so it looks like that server is not producing a valid response.

---

## Comment 8

> Username: jimhansson  
> Created at: 2017-07-04 15:14:52 UTC  
> Url: https://github.com/boostorg/beast/issues/592#issuecomment-312901958  

yes, that seems to be the problem, I have another issue i am going to mail them about, so I might as well include that too. considering this closed now. thanks for the help

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-07-04 15:17:26 UTC  
> Url: https://github.com/boostorg/beast/issues/592#issuecomment-312902522  

I'm kind of concerned that an industry standard ("cloudflare-nginx") doesn't send back valid HTTP

---

## Comment 10

> Username: ndusart  
> Created at: 2019-06-25 14:14:33 UTC  
> Url: https://github.com/boostorg/beast/issues/592#issuecomment-505464205  

@vinniefalco is there a way to make the parser more lenient about this or do we have to go back to use asio socket directly ?  
  
It can be blocking for interfacing with some non compliant servers and the status code is enough to determine the status of the response.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-06-25 16:21:19 UTC  
> Url: https://github.com/boostorg/beast/issues/592#issuecomment-505518108  

In the current version there is no way to make the parser more lenient but a future version may explore this.
