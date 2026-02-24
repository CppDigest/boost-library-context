# #1702 - websocket v1.70.0 issues with sslv3 alert handshake failure [Closed]

> Username: haashirashraf  
> Created at: 2019-09-13 15:27:27 UTC  
> Updated at: 2020-02-19 19:41:40 UTC  
> Closed at: 2019-09-13 22:01:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1702  

I wrote a persistent websocket connection using the beast version 1.70.0 websocket stream, when connecting to ws-feed.pro.coinbase.com on port 443 at the default endpoint of '/', the ssl handshake fails with the following error sslv3 alert handshake failure, the ssl context is initialised with ssl::context::tlsv12_client, it was working perfectly fine until very recently, could it be that using the ssl certificate provided in root_certificates from the repo is causing this? This issue has literally appeared overnight all else being equal as even the binaries running on the cloud can no longer start correctly, I can post code if required however the issue should be reproducible using the async-ssl example with the above websocket connection information.  
  
Thanks a lot guys!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-13 15:42:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1702#issuecomment-531288218  

Yeah you can't rely on **root_certificates.hpp** in the examples, that's just a massive hack I wrote to make the examples work. A real program has to check the operating-system specific certificate store and handle revocations, which is waaaaay beyond the scope of Beast.

---

## Comment 2

> Username: haashirashraf  
> Created at: 2019-09-13 16:06:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1702#issuecomment-531297139  

If I were to buy an alternate SSL certificate, what would be the steps to make it work, would it suffice to replace your hack with the new certificate between certificate begin and certificate end in the cert string?   
To clarify the web sockets are still successfully handshaking with other endpoints, it is only coinbase so far that has exhibited this behaviour.  
I actually have no idea as to how SSL actually works, but would I be correct to believe they could simply stop accepting this certificate?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-09-13 16:09:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1702#issuecomment-531298168  

root_certificates.hpp does not provide a client certificate, it provides root certificates which are used to validate that the server is who it says it is. Buying a new certificate is not going to help you. One option is to simply not validate the server's certificate. Or you can hard-code the server's certificate into your application and compare that they are equal. This is beyond the scope of Beast though, and I am by no means an expert, it was a pain just to get the SSL examples working locally.

---

## Comment 4

> Username: haashirashraf  
> Created at: 2019-09-13 16:14:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1702#issuecomment-531300007  

Okay Vinny thanks for clarifying, getting the examples up and running was more than enough! I'll try to find a work around, also note the regular handshake is also declined by the remote peer when using the standard WebSocket stream if this provides any additional information.

---

## Comment 5

> Username: haashirashraf  
> Created at: 2019-09-13 17:57:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1702#issuecomment-531333308  

The issue has been resolved, I didn't set the SNI hostname (I omitted this when upgrading to v1.70.0 from v1.67.0), thanks a lot Vinny!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-09-13 18:00:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1702#issuecomment-531334336  

\phew

---

## Comment 7

> Username: jrrpanix  
> Created at: 2020-02-19 19:01:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1702#issuecomment-588390333  

I'm having this issue on coinbase using websocket stream and cant figure out how to properly call   
            SSL_set_tlsext_host_name   
or even where to call it.  coinbase stopped working today on me but everything else works (bitstamp, kraken).  coinbase still works from python.  Does anyone have any example code for

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-02-19 19:12:09 UTC  
> Updated at: 2020-02-19 19:12:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1702#issuecomment-588395581  

Assuming `ws` is a `beast::websocket::stream<asio::ssl::stream<asio::ip::tcp::socket>>` ...  
  
The `ws.next_layer()` would yield a reference to the ssl stream.  
  
In which case you'd call:  
  
```  
if(! SSL_set_tlsext_host_name(ws.next_layer().native_handle(), host))  
{  
  // handle error here  
}  
```

---

## Comment 9

> Username: jrrpanix  
> Created at: 2020-02-19 19:41:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1702#issuecomment-588411194  

Thank you! that totally fixed the problem with coinbase! much appreciated!
