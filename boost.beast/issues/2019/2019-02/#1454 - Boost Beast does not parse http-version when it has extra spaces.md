# #1454 - Boost Beast does not parse http-version when it has extra spaces [Closed]

> Username: JanVerschaeren  
> Created at: 2019-02-15 12:01:55 UTC  
> Updated at: 2019-02-15 13:13:59 UTC  
> Closed at: 2019-02-15 13:13:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1454  

Hey,  
  
I have created an http server using boost beast. I can successfully send http-messages to it over the internet (also using boost beast). Now there is a 3rd party http-client that sends also messages to the server but i get a "Read: bad version" error.   
  
I managed to capture the packages of the 3rd party client using wireshark and tcpdump. I figured the error must be in the version part of the message. The packages seem normal at first, until i noticed a subtle difference. Instead of "[POST / HTTP/1.1\r\n]" it says "[POST /  HTTP/1.1\r\n]". There are two spaces before "HTTP".   
  
Now my question: is this a bug in boost beast that it cannot parse this or is it a malformed http message from this 3rd party app, according to the http-protocol?  
  
Greetings,  
Jan

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-15 12:10:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1454#issuecomment-464026038  

https://tools.ietf.org/html/rfc7230#section-3.1.1  
```  
request-line   = method SP request-target SP HTTP-version CRLF  
```  
  
RFC7230 is clear on this, there is exactly one space between the request-target and HTTP-version. Beast's HTTP parser is strict.
