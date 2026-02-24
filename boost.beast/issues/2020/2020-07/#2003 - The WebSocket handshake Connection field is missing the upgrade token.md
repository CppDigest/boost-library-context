# #2003 - The WebSocket handshake Connection field is missing the upgrade token [Closed]

> Username: iraizo  
> Created at: 2020-07-01 09:07:23 UTC  
> Updated at: 2024-06-06 05:34:19 UTC  
> Closed at: 2020-11-16 03:45:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2003  

When reporting a bug please include the following:  
  
### Version of Beast  
  
#define BOOST_BEAST_VERSION 292  
  
### Steps necessary to reproduce the problem  
This is what im trying to run  
https://github.com/iraizo/webcsgo-radar/blob/master/src/websocketserver/server.cpp  
  
### All relevant compiler information  
  
using Visual studio 2019  
  
The more information you provide the sooner your issue  
can get resolved!

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-07-01 10:07:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-652325884  

Hi @iraizo , Would you mind including a copy of the response headers you are receiving on the client?  
  
R

---

## Comment 2

> Username: iraizo  
> Created at: 2020-07-01 10:32:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-652337887  

> Hi @iraizo , Would you mind including a copy of the response headers you are receiving on the client?  
>   
> R  
  
will do, give me a minute

---

## Comment 3

> Username: iraizo  
> Created at: 2020-07-01 10:36:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-652339631  

@madmongo1   
  
<img src="https://jelbrek.icu/kc3854aq.png"></img>

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-07-01 11:51:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-652373050  

Looks like the response indicates an error in the request. Show the response line and body.

---

## Comment 5

> Username: iraizo  
> Created at: 2020-07-01 12:26:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-652387832  

> Looks like the response indicates an error in the request. Show the response line and body.  
  
the title is the response

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-07-01 12:38:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-652393467  

@iraizo He means please copy/paste the entire HTTP response that was sent back during the websocket handshake.  
  
Something like this:  
```  
HTTP/1.1 404 Not Found  
Content-Type: text/html  
Content-Length: 61  
  
<html><head></head><body><h1>404 Not Found</h1></body></html>  
```

---

## Comment 7

> Username: iraizo  
> Created at: 2020-07-01 13:06:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-652406771  

<img src="https://jelbrek.icu/kc3dib9g.png"></img>

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-07-01 13:18:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-652413276  

400 Bad Request says it all. The client has not formed a correct websocket handshake request.

---

## Comment 9

> Username: polhaghverdian  
> Created at: 2020-07-28 00:00:09 UTC  
> Updated at: 2020-07-28 00:08:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-664698563  

@madmongo1   
  
I have the same problem. Works with Firefox, but not Chrome.   
  
With exact same error message: **The WebSocket handshake Connection field is missing the upgrade token**  
  
Is this really enough? **ws.accept();** Or should i add the upgrade token manually?   
  
Any help will be appreciated!  
  
`      
    auto const address = net::ip::make_address("192.168.1.179");  
    auto const port = static_cast<unsigned short>(std::atoi("8080"));  
  
    // The io_context is required for all I/O  
    // net::io_context ioc{ 1 };  
    boost::asio::io_context ioc{ 1 };  
  
    // The SSL context is required, and holds certificates  
    ssl::context ctx{ ssl::context::tlsv13 };  
  
    // This holds the self-signed certificate used by the server  
    ctx.use_certificate_chain_file("./cert/cert.pem");  
    ctx.use_private_key_file("./cert/key.pem", boost::asio::ssl::context::pem);  
    // ctx.use_tmp_dh_file("..\\dh2048.pem");  
  
    ctx.set_verify_mode(ssl::verify_none); // ssl::verify_peer ssl::verify_none  
    // ctx.set_verify_callback(std::bind(&verify_certificate, std::placeholders::_1, std::placeholders::_2));  
  
    // The acceptor receives incoming connections  
    tcp::acceptor acceptor{ ioc, {address, port} };  
  
    // This will receive the new connection  
    tcp::socket socket{ ioc };  
  
    // Block until we get a connection  
    acceptor.accept(socket);  
  
    //  < ------------------------------------------------------------ -  
  
    // Construct the websocket stream around the socket  
    websocket::stream<beast::ssl_stream<tcp::socket&>> ws{ socket, ctx };  
  
    // Perform the SSL handshake  
    ws.next_layer().handshake(ssl::stream_base::server);  
  
    // Set a decorator to change the Server of the handshake  
    ws.set_option(websocket::stream_base::decorator(  
        [](websocket::response_type& res)  
        {  
            res.set(http::field::server,  
                std::string(BOOST_BEAST_VERSION_STRING) +  
                " websocket-server-sync-ssl");  
        }));  
  
    // Accept the websocket handshake  
    ws.accept();`

---

## Comment 10

> Username: madmongo1  
> Created at: 2020-07-28 07:49:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-664837896  

@polhaghverdian Are you able to share a minimal web page that I can run in firefox/chrome to replicate the problem locally?  
  
I'd also be interested to see any packets you've been able to collect with wireshark, if avaialble.

---

## Comment 11

> Username: polhaghverdian  
> Created at: 2020-07-28 14:41:36 UTC  
> Updated at: 2020-07-28 14:43:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-665081462  

@madmongo1    
  
btw: no error message when i connect using the IP address like this:   
`var serverConnection = new WebSocket('wss://192.168.1.179:8080');` instead of using "localhost".   
  
But i get another issue instead --> **Error: sslv3 alert certificate unknown**  
  
Do you want me to copy & paste the HTML/JS code here?

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-07-28 18:15:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-665196648  

> Do you want me to copy & paste the HTML/JS code here?  
  
If it's short. Otherwise a link to a git repo or gist is preferable.

---

## Comment 13

> Username: polhaghverdian  
> Created at: 2020-07-28 20:32:40 UTC  
> Updated at: 2020-07-28 20:33:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-665267962  

@madmongo1   
  
```  
<!DOCTYPE html>  
<html lang="en">  
<head>  
<meta charset="utf-8"/>  
<html>  
    <head>  
        <title>Web-client</title>  
        <link rel="stylesheet" href="/index.css">  
    </head>  
    <body>  
    </body>  
    <script type="text/javascript" src="/index.js"></script>  
</html>  
```  
  
  
  
```  
'use strict';  
  
// websocket connection  
var serverConnection = new WebSocket('wss://192.168.1.179:8080');  
  
// When websocket is open  
serverConnection.addEventListener('open', function (event) {  
      console.log("Socket opened");  
      serverConnection.send("hello!");  
});  
  
serverConnection.onmessage = function(e){  
   var server_message = e.data;  
   console.log(server_message);  
}  
```

---

## Comment 14

> Username: madmongo1  
> Created at: 2020-07-28 20:49:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-665275231  

Great, thanks.

---

## Comment 15

> Username: stale[bot]  
> Created at: 2020-08-29 10:13:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-683269353  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 16

> Username: stale[bot]  
> Created at: 2020-11-01 07:41:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-720048934  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 17

> Username: stale[bot]  
> Created at: 2020-11-16 03:45:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2003#issuecomment-727717715  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
