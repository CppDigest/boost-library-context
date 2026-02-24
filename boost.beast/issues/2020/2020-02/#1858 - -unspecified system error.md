# #1858 - "unspecified system error" [Closed]

> Username: elbaro  
> Created at: 2020-02-25 05:49:12 UTC  
> Updated at: 2020-04-26 16:25:43 UTC  
> Closed at: 2020-04-26 16:25:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1858  

`ws.async_read()` sometimes return an error where  
```  
ec.message() == "unspecified system error"  
```  
  
This is confusing and not helping me debug the problem.  
It can be a network problem, system thread limit, system socket limit, etc. I have no idea where this originates from. How can I get more error context?  
  
### Version of Beast  
`#define BOOST_BEAST_VERSION 277`  
  
### Steps necessary to reproduce the problem  
random  
  
### All relevant compiler information  
The error happens in gcc7 and gcc9

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-02-25 05:50:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1858#issuecomment-590696140  

Are you using SSL?

---

## Comment 2

> Username: elbaro  
> Created at: 2020-02-25 05:52:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1858#issuecomment-590696667  

yes.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-02-25 07:04:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1858#issuecomment-590716008  

This error is coming from SSL, there's nothing we can do about it.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-03-26 07:26:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1858#issuecomment-604272478  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: chreniuc  
> Created at: 2020-03-30 07:20:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1858#issuecomment-605827301  

@elbaro we also encountered this issue, one of my colleague did an investigation and it seemed that the problem was that the server was sending in text format(`ws.binary(false)`) a string that had this content:  
  
```xml  
<node1>  
    <node2 attr="0.xml" id="Äíåâíîé"/>  
    <node2 attr="1.xml" id="Âíóòðèäíåâíîé"/>  
    <node2 attr="2.xml" id="default"/>  
</node1>  
```  
  
When we've set the `ws` mode to [binary](https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/binary.html)(both in client and server) and this problem vanished. This might have something to do with the utf-8 encoding, but I can't say for sure. Hope this solves your problem.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-03-30 15:07:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1858#issuecomment-606057022  

> This might have something to do with the utf-8 encoding, but I can't say for sure.  
  
I can definitely say that whether you send or receive websocket messages in binary or text has no relevance to the "unspecified system error". This error comes from OpenSSL, right here:  
https://github.com/boostorg/asio/blob/95f7e936356810addf3a6b54dbd1129d10f3ba98/include/boost/asio/ssl/impl/error.ipp#L80

---

## Comment 7

> Username: chreniuc  
> Created at: 2020-03-30 17:05:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1858#issuecomment-606123370  

@vinniefalco I forgot to mention that we also use SSL.   
  
We noticed this behavior, when we send or receive that message in text, we get that error. When we switch to binary, that problem doesn't show up.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-03-30 17:14:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1858#issuecomment-606127742  

Perhaps the other end of the connection is terminating the connection because it wants binary not text. Or maybe you are sending invalid text (it must be a valid utf-8 encoded string), and the other end is terminating the connection. When a peer terminates a TLS/SSL connection incorrectly, it can cause you to receive errors.

---

## Comment 9

> Username: chreniuc  
> Created at: 2020-04-13 09:02:59 UTC  
> Updated at: 2020-04-13 09:12:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1858#issuecomment-612817888  

@vinniefalco   
  
Last week we had time to  investigate this thoroughly and indeed, we were sending text that was not  utf-8 valid, it was [cp1251](https://segfault.kiev.ua/cyrillic-encodings/#cp1251).  
  
This was our scenario:  
  
Client connects via secure websocket to the server and  sends text frames. The server adapts to what the client sends, if the client sends text frames, the server will also send text frames. Inside the server we were getting the content from the database which was a cp1251 encoded string and then send it to the client as a text frame. When the client received this message, the completion handler of `async_read` was called with the `unspecified system error` and closed the connection with the server. Inside the server the completion handler of `async_read` was called with the error code: `Connection closed at both ends`.  
  
We've switched to binary frames(initially we weren't aware that the information from the db that we were sending was not utf-8 valid).  
  
Thanks for taking the time and having the patience to address each comment that I posted in the beast repository for the past year :smile:

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-04-13 15:29:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1858#issuecomment-612949038  

Busted!! :)

---

## Comment 11

> Username: vinniefalco  
> Created at: 2020-04-13 15:30:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1858#issuecomment-612949343  

The myriad of character encoding schemes gives me a massive headache...

---

## Comment 12

> Username: vinniefalco  
> Created at: 2020-04-26 16:25:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1858#issuecomment-619579842  

This looks like it was resolved, I'm glad it is working for you now!
