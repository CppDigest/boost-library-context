# #257 - Parser non-deterministically fails to read body [Closed]

> Username: mika-fischer  
> Created at: 2017-02-07 09:05:04 UTC  
> Updated at: 2017-02-08 07:37:28 UTC  
> Closed at: 2017-02-08 00:11:51 UTC  
> Url: https://github.com/boostorg/beast/issues/257  

There seems to be a strange bug, when parsing header and body subsequently. Example code can be found here: [coroutine version](https://gist.github.com/mika-fischer/cb93b1071d9eea492f27234f7fb6d1b2) [callback version](https://gist.github.com/mika-fischer/a54a68769985afe0f22967881513cbb8).  
  
The problem manifests for body sizes larger than 64k. For body sizes smaller than that I've not been able to trigger the bug.  
  
So, generate a file with 64k or more, `dd if=/dev/zero of=test.bin bs=1k count=64`. Then use curl to post it to the server: `while curl --data-binary @test.bin -H 'Expect:' http://127.0.0.1:8081/; do; done;`  
  
Shortly, curl will exit with `curl: (55) Send failure: Connection reset by peer`.  
  
The output of a working request is:  
```  
New connection  
  
Waiting for request...  
Received headers:  
POST / HTTP/1.1  
Host: 127.0.0.1:8081  
User-Agent: curl/7.52.1  
Accept: */*  
Content-Length: 65536  
Content-Type: application/x-www-form-urlencoded  
  
Buffer size after headers: 361  
Body size: 65536  
  
Waiting for request...  
Error: End of file  
Connection closed  
```  
  
But often, beast will think there is no body or it has already read it. It will then immediately send the response and try to read another request, which fails, since the client is still sending the body data.  
  
```  
New connection  
  
Waiting for request...  
Received headers:  
POST / HTTP/1.1  
Host: 127.0.0.1:8081  
User-Agent: curl/7.52.1  
Accept: */*  
Content-Length: 65536  
Content-Type: application/x-www-form-urlencoded  
  
Buffer size after headers: 0  
Body size: 0  
  
Waiting for request...  
Error: bad method  
Connection closed  
```  
  
Any idea what's wrong here?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-02-07 12:41:51 UTC  
> Url: https://github.com/boostorg/beast/issues/257#issuecomment-277987634  

Thank you for your report. I am investigating. But if you want to try something real quick, try changing `asio::streambuf` to `beast::streambuf` and see if that fixes it. You'll have to include `<beast/core/streambuf.hpp>`

---

## Comment 2

> Username: mika-fischer  
> Created at: 2017-02-07 12:42:57 UTC  
> Url: https://github.com/boostorg/beast/issues/257#issuecomment-277987888  

I tried both already, actually. Same issue. Thanks for taking a look!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-02-07 12:43:38 UTC  
> Url: https://github.com/boostorg/beast/issues/257#issuecomment-277988074  

Does this happen only when the body is parsed separately? What if you read the entire message in one go?

---

## Comment 4

> Username: mika-fischer  
> Created at: 2017-02-07 12:48:16 UTC  
> Url: https://github.com/boostorg/beast/issues/257#issuecomment-277989323  

When I use only the `parser_v1` to parse everything in one go, everything works as expected.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-02-07 20:04:08 UTC  
> Url: https://github.com/boostorg/beast/issues/257#issuecomment-278123411  

We have tracked the problem down to a bug when a parser is constructed from the header parser. Working on a fix...

---

## Comment 6

> Username: mika-fischer  
> Created at: 2017-02-08 07:37:28 UTC  
> Url: https://github.com/boostorg/beast/issues/257#issuecomment-278252236  

Awesome, thanks, that fixed it!
