# #2235 - The Websocket handshake throws "bad version" exception [Closed]

> Username: horlm64  
> Created at: 2021-05-19 06:29:16 UTC  
> Updated at: 2021-05-19 14:50:10 UTC  
> Closed at: 2021-05-19 14:50:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2235  

When reporting a bug please include the following:  
  
Steps necessary to reproduce the problem  
- With "echo.websocket.org" website, there was no problem with this code.  
- With "192.168.1.1" website, the web socket handshake throws "bad version" exception.  
  (192.1681.1 is only available with chrome.)  
- As the "bad version" means the invalid HTTP-Version, I have tried to set the version as 11... Still, the issue has not been resolved.  
  
All relevant compiler information  
using Visual studio 2019

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-19 06:52:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-843799190  

Hi there, is there any chance you can put this code into a gist or a github repo so I can copy it exactly?  
  
The above cut/paste doesn't seem to have come out correctly.

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-05-19 07:00:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-843803960  

many thanks

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-05-19 07:09:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-843810360  

```  
/home/rhodges/cppalliance/issue-2235/cmake-build-debug/issue_2235  
Hello World!   
  
  
Process finished with exit code 0  
```  
  
Works fine here.

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-05-19 07:10:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-843811120  

This looks a bit suspect. Why are you using unique_ptr?  
  
```  
            std::unique_ptr<std::string> version = boost::make_unique<std::string>("11");  
            req.set(http::field::version, *version);  
```

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-05-19 07:33:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-843830351  

In fact, out of interest, why are you setting this field?   
I can't find it listed on MDN (and now I'm wondering why we have it in the enum)

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-05-19 07:39:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-843834410  

Comments inline   
  
```  
    ws.set_option(websocket::stream_base::decorator(  
        [](websocket::request_type& req)  
        {  
        /* - this looks wrong  
            std::unique_ptr<std::string> version = boost::make_unique<std::string>("11");  
            req.set(http::field::version, *version);  
              
            There is no need to allocate a string on the heap only to throw it away.  
            You can simply write:  
              
            req.set(http::field::version, "11");  
              
            However this still has no effect. The request object is already version 1.1 and this  
            gets sent in the first line of the HTTP request. If you want to set the HTTP version to 1.1  
            you would write:  
  
             req.version(11);  
               
             But there is no need. The HTTP request is already version 1.1 by default.  
        */  
        }));  
```

---

## Comment 7

> Username: horlm64  
> Created at: 2021-05-19 08:42:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-843884768  

According to the instructions in the boost website,i.e. Web socket, and RPC 6455, the user-agent, host and port would be critical for handshake. Additionally, as such error("Bad-Version") occurs, I assume that the current HTTP-Version doesn't correspond to the one for this website. For this reason, I set some fields and try to figure out the resolution. I would implement the way you suggested, and make a comment again.

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-05-19 08:47:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-843889478  

The program you posted works on my machine. It seems correct.

---

## Comment 9

> Username: horlm64  
> Created at: 2021-05-19 08:48:56 UTC  
> Updated at: 2021-05-19 08:50:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-843890722  

When it doesn't work with my laptop and another laptop in the uni, then could it be a problem with the machine?

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-05-19 08:50:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-843891784  

In your shoes the next thing I would do is look at the actual HTTP traffic on the wire with wireshark.  
Are you connecting through a proxy?

---

## Comment 11

> Username: horlm64  
> Created at: 2021-05-19 08:52:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-843893548  

As I am a swimmer in this field, could you let me know how to check if I am connecting through a proxy?

---

## Comment 12

> Username: madmongo1  
> Created at: 2021-05-19 08:56:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-843897012  

On windows you’d look at the internet settings in the control panel (or whatever they call it these days)

---

## Comment 13

> Username: horlm64  
> Created at: 2021-05-19 09:11:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-843908967  

Yes, I just have a look at the setting. Currently, I am connecting through a proxy

---

## Comment 14

> Username: madmongo1  
> Created at: 2021-05-19 10:45:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-843980573  

OK. The code you have written assumes it's connecting direct. HTTP proxies are beyond the scope of boost.beast and asio.  
What is happening is that your computer is sending the traffic to the proxy as if it were the server you want.   
The proxy is responding, not the websocket server.  
Proxies can be SOCKS protocol or they can be HTTP proxies.  
They handle the work differently but offer the same broad principle in that they make the connection on your behalf.   
Each has a different protocol. If you are fortunate and the proxy is an HTTP proxy, then sending requests through it should be "straightforward" as it's a variation on the HTTP theme.  
Whichever proxy type it is, the communication with the proxy is beyond the low-level scope of beast.

---

## Comment 15

> Username: horlm64  
> Created at: 2021-05-19 11:35:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-844016750  

I would figure out the further work, and I appreciate your feedback.

---

## Comment 16

> Username: madmongo1  
> Created at: 2021-05-19 14:48:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-844178187  

May we close the issue for now, since it's not an issue with Beast per se?

---

## Comment 17

> Username: horlm64  
> Created at: 2021-05-19 14:50:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2235#issuecomment-844180038  

Sure, I agree...
