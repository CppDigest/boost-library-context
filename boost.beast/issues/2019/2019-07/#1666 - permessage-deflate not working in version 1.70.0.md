# #1666 - permessage-deflate not working in version 1.70.0 [Closed]

> Username: vcouvert  
> Created at: 2019-07-24 07:15:34 UTC  
> Updated at: 2019-07-29 20:16:19 UTC  
> Closed at: 2019-07-29 19:03:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1666  

Hi,  
  
I'm using version 1.70.0 and it seems that websocket compression does not work even when setting the right options before calling async_accept:  
    boost::beast::websocket::permessage_deflate opt;  
    opt.client_enable = true;  
    opt.server_enable = true;  
    opt.compLevel = 3;  
    ws.set_option(opt);  
  
I use Wireshark to check compression and the only way I found to make compression work was to force wr_compress to true in stream_impl.hpp (Line 102).  
  
Did I miss anything?  
  
Thanks for your help and your work on Beast!

---

## Comment 1

> Username: djarek  
> Created at: 2019-07-27 19:58:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1666#issuecomment-515709618  

Does the client offer that it supports compression in the upgrade request?

---

## Comment 2

> Username: vcouvert  
> Created at: 2019-07-29 07:26:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1666#issuecomment-515877639  

Hi,  
  
The client is Google Chrome so I think it supports compression but perhaps I'm wrong.  
What I don't understand is the fact that I need to modify the code of Beast to enable compression.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-07-29 16:17:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1666#issuecomment-516059502  

> What I don't understand is the fact that I need to modify the code of Beast to enable compression  
  
That sounds weird, I am investigating.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-07-29 17:36:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1666#issuecomment-516089400  

Big bug, thanks for the report!

---

## Comment 5

> Username: vcouvert  
> Created at: 2019-07-29 20:16:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1666#issuecomment-516145450  

Thanks for your work and this fix!
