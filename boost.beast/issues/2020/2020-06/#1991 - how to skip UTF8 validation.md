# #1991 - how to skip UTF8 validation [Closed]

> Username: Alchemist2077  
> Created at: 2020-06-19 13:08:46 UTC  
> Updated at: 2020-08-01 09:00:26 UTC  
> Closed at: 2020-08-01 09:00:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1991  

Hello.  
  Sometimes,Websocket server send binary data with "text" opcode.  
  Can i do something to skip UTF8 validation by utf8_checker?  
  I know Server sending binary data with text opcode is a bad bahavior,but is very diffcult to change service bahavior.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-06-19 16:25:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1991#issuecomment-646729821  

We are reluctant to break conformance with RFC 6455 without very good reason.  
  
Beast has been in production for at least 4 years and we have not had this problem reported before.  
  
Are you able to provide an example of such a non-conforming service?  
  
It may help your case.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-06-21 18:09:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1991#issuecomment-647161858  

No response to request.  
Closing issue.

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-06-21 20:50:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1991#issuecomment-647180073  

Re-opening to provide time for more feedback. @Alchemist2077

---

## Comment 4

> Username: Alchemist2077  
> Created at: 2020-06-22 02:12:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1991#issuecomment-647227561  

Server using openresty/1.15.8.2 ,and openresty native library as websocket server.  
Sending text data code looks like:  
    `ngx.ctx.svr:send_text(_encrypt_result(retdata))`  
  "_encrypt_result" mean encrypt data using sha1. So text data translate to binary data.  
  
 When beast cliant try to recive data from this server using:  
```  
  ws_.async_read(buffer_, beast::bind_front_handler(&WebsocketSession::on_read,  
                                                    shared_from_this()));  
```  
  
 I got an error says:  
  "The WebSocket frame payload was not valid utf8"  
  
By the way: I have read the RFC 6455 standerd,and i decide to motify openresty server logic to send data with proper opcode.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2020-07-25 03:51:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1991#issuecomment-663803537  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2020-08-01 09:00:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1991#issuecomment-667498890  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
