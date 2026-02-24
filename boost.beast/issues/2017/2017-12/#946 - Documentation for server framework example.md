# #946 - Documentation for server framework example? [Closed]

> Username: DragonOsman  
> Created at: 2017-12-22 21:34:14 UTC  
> Updated at: 2017-12-30 15:58:24 UTC  
> Closed at: 2017-12-30 15:58:24 UTC  
> Url: https://github.com/boostorg/beast/issues/946  

I'm thinking of learning to use Beast for an app I want to write, but it might take me more time than necessary if I also learned ASIO (since I also need to learn that), so I thought I'd just try to learn the server framework on awesome-cpp.  But I can't find any documentation for it, and it's also only a web server framework.  So there are two things I need to ask:  
1. Where's the documentation for that example?  
2. Would one of the clients that comes with Beast as an example be good to use with the framework?  
  
Thanks in advance for any help.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-22 21:39:18 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-353678615  

The server-framework was available in an older version of Beast and is no longer supported or recommended. It has been replaced by a vast array of new, smaller examples. All of the clients and servers in the new examples are suitable for use it just depends on what style you want. Pick one of them that meets your needs:  
  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/examples.html

---

## Comment 2

> Username: DragonOsman  
> Created at: 2017-12-23 23:20:21 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-353753737  

Ah, okay, thanks for the info.

---

## Comment 3

> Username: DragonOsman  
> Created at: 2017-12-24 16:47:11 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-353793794  

Where can I find documentation on the examples?  If it's okay to ask.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-12-24 17:05:14 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-353794551  

Sure, the examples are loaded with comments throughout. If you find a piece of code in the examples that you don't understand just let me know and I'll add some more comments to it.

---

## Comment 5

> Username: DragonOsman  
> Created at: 2017-12-25 15:03:20 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-353874640  

Alright, thanks.    
  
So can I just take and use a server and client from the examples in a web app?  Or would it be better to write my own after understanding the examples and Beast itself?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-12-25 15:07:17 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-353874828  

The examples are written so that they can be used as a starting point for your client or server application.

---

## Comment 7

> Username: DragonOsman  
> Created at: 2017-12-26 22:33:57 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-354020442  

So that means I can just take and use them as is?  Or does it just mean I can use it to understand how to write my own clients and servers to use in an application?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-12-26 23:02:06 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-354022773  

>So that means I can just take and use them as is?  
  
You can make a copy of the source file and use that as a starting point.

---

## Comment 9

> Username: DragonOsman  
> Created at: 2017-12-27 14:12:34 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-354119455  

Alright, thanks.  
  
Now this is probably a noob question, but: what exactly is a flex server or client?    
  
And lastly, which of the HTTP examples supports both the GET and POST verbs, at least?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-12-27 14:47:11 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-354124052  

>this is probably a noob question, but: what exactly is a flex server or client?  
  
No, not a noob question. A "flex server" is my own name for a server which can do plain HTTP or handshake using SSL on the same listening port. There's no "flex client."  
  
>which of the HTTP examples supports both the GET and POST verbs, at least?  
  
All the HTTP client and server examples support "GET" and none of them support "POST." Send or receiving a POST request is no different than handling any other message though, so it should not be difficult to implement. If you have a specific question, I can try to answer it.

---

## Comment 11

> Username: DragonOsman  
> Created at: 2017-12-29 16:07:01 UTC  
> Updated at: 2017-12-29 16:07:29 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-354464754  

I'm guessing I could easily extend the example server and client I choose to use to also support POST requests.  If so, I'll just have to learn how to use Beast well enough for that.  
  
Could you maybe include that into the examples in the next release of Beast, if it's not too much trouble (and hopefully that version will also be in Boost later)?  I think people would want that.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-12-29 16:14:56 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-354465796  

I mean how hard is it to add  
  
```  
if(req.method() == http::verb::post)  
{  
    // do something  
}  
```  
  
To this function?  
https://github.com/boostorg/beast/blob/617fc428799bf3684c996d500068dbeacd8f5877/example/advanced/server/advanced_server.cpp#L109  
  
>Could you maybe include that into the examples in the next release of Beast  
  
I don't think that belongs in Beast. First of all how would users test it? You can't just easily force your browser to send a POST request to your server. And if you're sending a POST from a client, to which service will you send it to for testing?  
  
Beast is a low-level library and makes certain assumptions about the level of skill required of users. For example, a knowledge of Boost.Asio is required to use Beast.  POST messages are handled the same way as any other message in Beast, there is nothing magical about them other than that they have a different method (`http::method::post` instead of `http::method::get`). You should not need a specific example of handling POST messages in Beast to understand that.

---

## Comment 13

> Username: DragonOsman  
> Created at: 2017-12-29 16:36:05 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-354468478  

Alright, I got it.    
  
One more thing, though: I've installed OpenSSL headers and .lib files, but I don't know how to use it for HTTPS.  Does the library take care of it after I #include the headers and link in the library files (and define the macro for detecting OpenSSL support)?

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-12-29 16:59:58 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-354471724  

>Does the library take care of it  
  
No. If you want HTTP/S support in your application, you need to write code for it. This is demonstrated in the example programs which use SSL:  
  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/examples.html

---

## Comment 15

> Username: DragonOsman  
> Created at: 2017-12-29 17:06:03 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-354472503  

Well, yes, but I meant was if I have to do anything else other than writing that code.  I need the certificate, right?  What else do I need to do aside from writing the code?

---

## Comment 16

> Username: vinniefalco  
> Created at: 2017-12-29 17:19:38 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-354474161  

Well you have to secure your server...

---

## Comment 17

> Username: DragonOsman  
> Created at: 2017-12-30 15:42:59 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-354552548  

Okay, I get what I need to do for that.  How do I mark this as solved?

---

## Comment 18

> Username: vinniefalco  
> Created at: 2017-12-30 15:53:47 UTC  
> Url: https://github.com/boostorg/beast/issues/946#issuecomment-354553099  

Press the "Close" button at the bottom
