# #2020 - Unable to open connection to WWS:// socket with sub path [Closed]

> Username: jakepenn  
> Created at: 2020-07-18 00:23:35 UTC  
> Updated at: 2020-07-20 20:00:12 UTC  
> Closed at: 2020-07-20 20:00:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2020  

Version:   
  
290  
  
Issue:   
  
I've a working web socket I'm able to easily connect to using javascript, but am unable to connect to using beast/boost  
  
### Steps necessary to reproduce the problem  
  
this works great:   
  
        std::string const host = "echo.websocket.org";  
        std::string port = "80";  
          
        boost::asio::io_context ioc;  
        boost::asio::ip::tcp::resolver resolver { ioc } ;  
        boost::asio::ip::tcp::socket sock { ioc };  
        boost::asio::connect(sock, resolver.resolve(host, port));  
        boost::beast::websocket::stream<boost::asio::ip::tcp::socket&> ws(sock);  
  
However, this doesn't connect:   
  
        std::string const host = "9m77tq4a3j.execute-api.us-east-1.amazonaws.com/prod";  
        std::string port = "443";  
          
        boost::asio::io_context ioc;  
        boost::asio::ip::tcp::resolver resolver { ioc } ;  
        boost::asio::ip::tcp::socket sock { ioc };  
        boost::asio::connect(sock, resolver.resolve(host, port));  
        boost::beast::websocket::stream<boost::asio::ip::tcp::socket&> ws(sock);  
  
I'm wondering what is the correct way to configure the resolver in order to connect to a more complex web address with subdomains and paths. In the npm package wscat connection is possible through only the full URL with protocol:  
  
wws://9m77tq4a3j.execute-api.us-east-1.amazonaws.com/prod  
  
Is there a similar way to connect through boost/asio/beast -- apologies if guides are out there for this but I've been struggling to fully understand how all these components come together.  
  
Thanks,   
  
Jake

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-07-18 00:27:50 UTC  
> Updated at: 2020-07-18 00:28:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2020#issuecomment-660392045  

`9m77tq4a3j.execute-api.us-east-1.amazonaws.com/prod` is not a valid host.  
  
The correct host is `9m77tq4a3j.execute-api.us-east-1.amazonaws.com`  
  
`/prod` is the path for accessing the resource, which should not be passed to the resolver.

---

## Comment 2

> Username: jakepenn  
> Created at: 2020-07-18 17:53:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2020#issuecomment-660517897  

Okay so if I understand correctly you can't mount a wss service at a resource path and use it with beast? Or is there a way to do this? I've attempted passing the host name without the path to the resolver & 443 for the port, and it's still not been able to connect.  
  
Is my best bet to redeploy the socket at the index / root layer of the web app?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-07-18 18:29:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2020#issuecomment-660522528  

Yes you can connect and make this work but Beast requires you to already understand and know how to use Asio. And if you are going to make secure WebSocket connections (implied by "wss") then you are expected to already understand how to use Asio's SSL streams. This Beast example implements a TLS-aware WebSocket client, which is similar to what you are trying to do:  
  
https://github.com/boostorg/beast/tree/develop/example/websocket/client/sync-ssl

---

## Comment 4

> Username: jakepenn  
> Created at: 2020-07-20 20:00:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2020#issuecomment-661302744  

Thanks Vinnie, I wasn't aware the implications of the SSL connection entailed a different methodology than a standard ws, I'll go through and review the ASIO SSL docs and then reattempt later. Thanks for the pointers! Gonna close this for now.   
  
Best,  
  
J
