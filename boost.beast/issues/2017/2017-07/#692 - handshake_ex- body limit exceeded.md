# #692 - handshake_ex: body limit exceeded [Closed]

> Username: matthijs  
> Created at: 2017-07-28 14:36:13 UTC  
> Updated at: 2017-07-29 19:50:55 UTC  
> Closed at: 2017-07-29 19:50:55 UTC  
> Url: https://github.com/boostorg/beast/issues/692  

Repository starred ;-)  
  
When reporting a bug please include the following:  
  
### Version of Beast  
  
```  
matthijs % git log -1  
commit e5ab4a11142761fdef33410685afe41b08449549 (HEAD -> master, origin/v85-pre-boost, origin/v85, origin/master, origin/develop, origin/HEAD)  
Author: Vinnie Falco <vinnie.falco@gmail.com>  
Date:   Sat Jul 22 05:32:15 2017 -0700  
  
    Set version to 85  
```  
  
### Steps necessary to reproduce the problem  
  
I cannot provide a small working example because it is behind an authenticated channel. Let me explain.  
  
I have a websocket client which connects to the server. Everything goes fine until the response is returned. After some debugging I found out that the 'Content-Length' set by the server is rather large (2GB). So the error is set to 'body limit exceeded' because there is an limit on how big the response should be.  
  
client -> sends upgrade request  
server -> response with a Content-Length: 2147483648  
client -> sets error: 'body limit exceeded'  
  
And here are the response headers from the server:  
HTTP/1.1 101  
Upgrade: websocket  
Sec-WebSocket-Version: 13  
Sec-WebSocket-Protocol: JSON  
Sec-WebSocket-Accept: response-key  
Content-Length: 2147483647  
Content-Type: application/json; charset=UTF-8  
Access-Control-Allow-Origin: *  
Connection: Upgrade  
Proxy-Connection: keep-alive  
Pragma: no-cache  
Cache-Control: no-cache, no-store, must-revalidate  
Expires: Thu, 1 Jan 1970 00:00:00 GMT  
Strict-Transport-Security: max-age=31536000; includeSubDomains  
  
Is there a way to increase or ignore the 'Content-Length' header on an upgrade request?  
  
### All relevant compiler information  
  
Compiler:  
matthijs % clang++-3.9 -v  
clang version 3.9.1-10 (tags/RELEASE_391/rc2)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
  
Compiler options: -stdlib=libc++ -std=gnu++14  
  
Regards, Matthijs

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-28 14:55:19 UTC  
> Url: https://github.com/boostorg/beast/issues/692#issuecomment-318674109  

Thank you for your detailed report. Its a funny coincidence but 2147483647 happens to be 0x7fffffff. This looks like a bug in the server. An HTTP Upgrade message specifying a Content-Length is invalid. Beast should give you a different error message. Unfortunately this won't solve your problem.  
  
I see there's no "Server" field there, are you able to reveal the type of server or is that confidential? Can you contact the server operator or software provider to see if they can shed light on why they feel it is necessary to send "Content-Length" of 0x7fffffff ?

---

## Comment 2

> Username: matthijs  
> Created at: 2017-07-28 18:13:41 UTC  
> Url: https://github.com/boostorg/beast/issues/692#issuecomment-318725621  

I can't reveal the type of server because I don't know. And sorry I don't know the server-operator and/or the software provider.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-28 18:49:47 UTC  
> Updated at: 2017-07-28 18:50:33 UTC  
> Url: https://github.com/boostorg/beast/issues/692#issuecomment-318733981  

I don't see an easy way to fix this, because Content-Length in an Upgrade message is malformed. And what if the server really tries to send 2GB? We can't allow that because it enables denial of service attacks on Beast websocket servers.  
  
Even if the caller is able to raise the limit, the HTTP read algorithm is still going to try to read the amount of data promised by the Content-Length. As I doubt that the server is going to deliver 2GB or more worth of data, the HTTP read call will hang forever.  
  
What you are asking is a way to inform the parser used in the websocket stream implementation to simply ignore the Content-Length. This is highly irregular and I don't see a way to do this cleanly. What is the URL for the service you are trying to use? We really have to get to the bottom of why they are generating invalid HTTP messages.

---

## Comment 4

> Username: matthijs  
> Created at: 2017-07-29 11:22:32 UTC  
> Url: https://github.com/boostorg/beast/issues/692#issuecomment-318823172  

Ok, thank you for the information, I'll try to contact the server operator and ask them for more information on why they don't follow the standard here.  
  
I have adjusted the basic parser a bit, so that the content length (_len) is set to 0. And after that I could succesfully setup a websocket link.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-29 12:26:50 UTC  
> Url: https://github.com/boostorg/beast/issues/692#issuecomment-318827926  

Please verify that this branch resolves your issue https://github.com/vinniefalco/beast/commits/v93

---

## Comment 6

> Username: matthijs  
> Created at: 2017-07-29 14:14:16 UTC  
> Url: https://github.com/boostorg/beast/issues/692#issuecomment-318833675  

Yes, this fixed my issue. Thank you very much!

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-07-29 14:19:53 UTC  
> Url: https://github.com/boostorg/beast/issues/692#issuecomment-318834094  

Great!!!
