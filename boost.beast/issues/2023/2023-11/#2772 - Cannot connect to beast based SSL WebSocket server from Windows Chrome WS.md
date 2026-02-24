# #2772 - Cannot connect to beast based SSL WebSocket server from Windows Chrome WS... [Closed]

> Username: Honya2000  
> Created at: 2023-11-27 09:49:41 UTC  
> Updated at: 2023-12-08 10:56:27 UTC  
> Closed at: 2023-12-08 10:56:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2772  

Hello,  
  
I'm creating basic SSL WebSocket client / server app.  
The source code is basically the same as in beast examples: async-ssl for both client and server.  
For the server i'm using CA certificate i obtained recently....  
  
 I could esablish connection and send the data from beast client, Win Firefox, iOS (both Safari and Chrome).  
But it doesn't work completely from Windows Google Chrome.   
Chrome WS just reports in the console:  
WebSocket connection to 'wss://hon.....org:9090/svc' failed  
  
While server throws error on first read operation:  
read: stream truncated  
  
I have spent 2 days trying to make it working with Chrome without success.  
Tried all the options on the server side here:  
                ssl::context ctx{ ssl::context::tlsv13 };  
  
                ctx.set_default_verify_paths();  
  
                ctx.set_options(  
                    boost::asio::ssl::context::default_workarounds |  
                    //boost::asio::ssl::context::no_compression |  
                    boost::asio::ssl::context::no_sslv2 |  
                    boost::asio::ssl::context::no_sslv3 |  
                    //boost::asio::ssl::context::no_tlsv1 |  
                    boost::asio::ssl::context::verify_none  
                    //boost::asio::ssl::context::single_dh_use  
                );  
  
Nothing works.   
First read in this sequence just fails with zero size buffer...  
  
                void on_accept(beast::error_code ec)  
                {  
                    if (ec)  
                        return fail(ec, "accept");  
  
                    // Read a message  
                    do_read();  
                }  
  
                // Read chain  
                void do_read()  
                {  
                    // Read a message into our buffer  
                    ws_.async_read(buffer_, beast::bind_front_handler(&wsSession::on_read, shared_from_this()));  
                }  
Not sure why this read happens at all, since client just fails to connect - it doesn't send anything!  
When i debug the server connection from Firefox - first data server gets on read - is what the client actually sent.  
But Chrome doesn't sent anything....  
  
  
My previous SSL WS server based on civetweb lib - is working fine with windows chrome.  
But if i'm trying to use websocketpp based server or beast (both are based on the same asio) - it doesn't work with the same truncated stream error.  
  
I got out of ideas.  
Any help will be appreciated!

---

## Comment 1

> Username: ashtum  
> Created at: 2023-11-27 10:26:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1827557482  

@Honya2000, if I understand correctly, it seems that when you connect using Chrome, the code reaches the `on_accept()` function without encountering any errors. This suggests that both the SSL handshake and Websocket handshake are performed without any issues.  
You might be able to obtain more information about the error in the `Network` tab of Chrome DevTools.

---

## Comment 2

> Username: Honya2000  
> Created at: 2023-11-27 11:12:16 UTC  
> Updated at: 2023-11-27 11:12:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1827627724  

Yes, server got on_accept without errors.  
Then first read gives truncated stream error, Second on_read gives operation_aborted.  
  
Looks some http / websocket negotiation just fails, probably headers parsing, not sure.  
Pretty sure will get the same error if i would compile websocket example in beast, since i'm using the same code.  
  
Checked Chrome console - it shows request header, but doesn't show Received header (seems nothing was received).  
Request header looks like this:  
  
Accept-Encoding: gzip, deflate, br  
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8,ru;q=0.7,de;q=0.6  
Cache-Control: no-cache  
Connection: Upgrade  
Host: h...org:9090  
Origin: http://h....org:88  
Pragma: no-cache  
Sec-Websocket-Extensions: permessage-deflate; client_max_window_bits  
Sec-Websocket-Key: BjKTdimxZCmtJKTvOQBovw==  
Sec-Websocket-Protocol: text  
Sec-Websocket-Version: 13  
Upgrade: websocket  
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Safari/537.36

---

## Comment 3

> Username: Honya2000  
> Created at: 2023-11-27 11:56:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1827690787  

Checked Network tab in Firefox:  
  
Firefox sent this header to the server:  
Accept	*/*  
Accept-Encoding	gzip, deflate, br  
Accept-Language	en-US,en;q=0.5  
Cache-Control	no-cache  
Connection	keep-alive, Upgrade  
Host	h...org:9090  
Origin	http://h....org:88  
Pragma	no-cache  
Sec-Fetch-Dest	empty  
Sec-Fetch-Mode	websocket  
Sec-Fetch-Site	cross-site  
Sec-WebSocket-Extensions	permessage-deflate  
Sec-WebSocket-Key	8iEu4Dxyny2tnyuu6bwZjQ==  
Sec-WebSocket-Protocol	text  
Sec-WebSocket-Version	13  
Upgrade	websocket  
User-Agen	Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:120.0) Gecko/20100101 Firefox/120.0  
  
And receives this response header:  
Connection	Upgrade  
Sec-WebSocket-Accept	qSELOVvSql0pTxscZ7OFHOiwXFI=  
Server	Boost.Beast/351 Tmrw-Service  
Upgrade	websocket  
  
The only noticeable difference is that Firefox uses keep-alive attribute for connection, while Chrome - not.

---

## Comment 4

> Username: ashtum  
> Created at: 2023-11-27 12:21:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1827732787  

> Pretty sure will get the same error if i would compile websocket example in beast, since i'm using the same code.  
  
I highly recommend starting with the websocket_server_async_ssl example in the example directory. I'm using Chrome and had no problem connecting to the example server.

---

## Comment 5

> Username: Honya2000  
> Created at: 2023-11-27 14:13:21 UTC  
> Updated at: 2023-11-27 14:41:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1827912388  

ok, i finally was able to compile beast with SSL, though was not easy due to OpenSSL dependecy.  
And as expected i got the same stream truncated error in websocket-server-async-ssl example.  
  
The only change i made to this example was related to SSL context setup:  
  
    // This holds the self-signed certificate used by the server  
    //load_server_certificate(ctx);  
  
    ctx.set_default_verify_paths();  
  
    ctx.set_options(  
      ssl::context::default_workarounds |  
      //boost::asio::ssl::context::no_compression |  
      ssl::context::no_sslv2 |  
      ssl::context::no_sslv3 |  
      ssl::context::no_tlsv1  
      //ssl::context::verify_none  
      //boost::asio::ssl::context::single_dh_use  
    );  
  
    ctx.use_certificate_chain_file("server_ca.pem");  
    ctx.use_private_key_file("server_ca.pem", ssl::context::pem);  
  
But.. error isn't related to SSL since handshake accepted.  
It is visible (in beast/websocket/impl/read.hpp) that websocket code tries to parse server header and somehow fails, and instead of sending responce header to the server - it just calls on-read session callback with the error code.

---

## Comment 6

> Username: Honya2000  
> Created at: 2023-11-27 14:21:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1827929316  

Btw if I remove my SSL cerificate related code, i cannot even connect to the server from Chrome.  
Server just throws the error:  
handshake: sslv3 alert certificate unknown (SSL routines, ssl3_read_bytes)  
  
So how did you test it with the default example?

---

## Comment 7

> Username: Honya2000  
> Created at: 2023-11-27 14:29:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1827945001  

Looks like the same issue was reported several month ago...  
https://github.com/emscripten-core/emscripten/issues/19100  
But looks like still no solution :(

---

## Comment 8

> Username: Honya2000  
> Created at: 2023-11-28 10:47:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1829564878  

Ok, I replaced OpenSSL for beast to the newest one OpenSSL 3.3 (spent several hours to compile it). Thought probably some old openssl functionality doesn't work properly.  
Unfortunately this didn't change anything. Still getting the same error: stream truncated from chrome.

---

## Comment 9

> Username: ashtum  
> Created at: 2023-11-28 11:20:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1829622567  

@Honya2000, I have no idea how to replicate the issue. are you suggesting that connecting to `websocket_server_async_ssl` with a connection from Chrome on a Windows machine should give a similar error?

---

## Comment 10

> Username: Honya2000  
> Created at: 2023-11-28 12:19:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1829730239  

Yes, as i mentioned yesterday - unmodified example websocket_server_async_ssl - doesn't work with chrome due to ssl certificate issue.  
If i'm using my certificate in the example - it gives the same error as in my project with truncated stream.  
  
And it happens only on desktop chrome, both windows and mac (mobile iOS chrome is working).  
Even iOS is working which is definitely doesn't accept self signed certificate.

---

## Comment 11

> Username: Honya2000  
> Created at: 2023-11-28 12:22:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1829734853  

When i analyze the websocket/read.hpp different functions - i see it reads the first upgrade header from the server. But have no clue why it doesn't send responce to the server, but call on_read callback instead.  
  
Trying to use boost log to log internal events, but cannot compile it due to hundreds boost libs dependencies...

---

## Comment 12

> Username: ashtum  
> Created at: 2023-11-28 14:33:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1829962524  

I've just tested it with Chrome on Windows and it is working fine. Have you tried visiting the HTTPS page first, for example, https://localhost:8080/, and granting permission to access an unsafe page?

---

## Comment 13

> Username: Honya2000  
> Created at: 2023-11-28 16:14:55 UTC  
> Updated at: 2023-11-28 16:16:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1830191759  

This is my server: https://honya.myftp.org:9090/  
  
When I enter it from Chrome - I get:  
The WebSocket handshake Connection field is missing the upgrade token  
  
Chrome doesn't complain about unsafe page since i'm using CA certificate. (otherwise iOS wouldn't work)

---

## Comment 14

> Username: ashtum  
> Created at: 2023-11-28 16:33:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1830255978  

> When I enter it from Chrome - I get:  
> The WebSocket handshake Connection field is missing the upgrade token  
  
This is expected because the server treats every connection as a WebSocket connection and expects an HTTP upgrade request. I just wanted to see if there is any issue with the certificate.  
  
> This is my server: https://honya.myftp.org:9090/  
  
I can confirm it is impossible to connect from Chrome in windows. Please use the websocket_server_async_ssl example on the server side without any modifications and check if the problem persists.

---

## Comment 15

> Username: Honya2000  
> Created at: 2023-11-28 16:41:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1830269490  

Please read my messages above. Yesterday i tried unmodified example and ssl handshake failed because of certificate issues in the example.  
  
With my certificate - i got the same truncated message error.  
But it is not my certificate issue because:  
1. iOS, Android, and desktop firefox clients are working with my server.  
2. Desktop Chrome can connect to my server based on different websocket backend (civetweb) using the same certificate.  
  
All asio based servers are fail to accept connections from Desktop Chrome (tested websocketpp, and beast)

---

## Comment 16

> Username: ashtum  
> Created at: 2023-11-28 16:46:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1830278099  

What platform does your server use?  
I'm using Linux for running websocket_server_async_ssl example and it works fine when I connect from Chrome in Windows.

---

## Comment 17

> Username: Honya2000  
> Created at: 2023-11-28 16:48:01 UTC  
> Updated at: 2023-11-28 16:48:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1830280907  

i'm on windows 10.  
Probably this is the issue? beast just doesn't work properly on windows?

---

## Comment 18

> Username: Honya2000  
> Created at: 2023-11-28 16:51:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1830286847  

tomorrow will try to adjust chrome security setting to fix certificate issues with unmodified example.  
But doubt it will fix anything...

---

## Comment 19

> Username: ashtum  
> Created at: 2023-11-28 17:03:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1830307396  

I don't think it is a beast issue, because we run CI in Windows platforms too.  
Is it possible for you to test another platform and report the results?  
Tomorrow I'll try to replicate the server in a Windows platform.

---

## Comment 20

> Username: Honya2000  
> Created at: 2023-11-28 17:12:57 UTC  
> Updated at: 2023-11-28 17:15:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1830324063  

It could be beast or asio. If the issue is reproducible in websocketpp i would assume the issue is in asio, since it uses asio only.  
Testing on another platform wouldn't be easy right now because i need to install linux on windows machine and then compile lot of projects there including OpenSSL.  
Much easier would be to connect chrome to unmodified async-ssl websocket example.

---

## Comment 21

> Username: Honya2000  
> Created at: 2023-11-28 22:21:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2772#issuecomment-1830852053  

Ok, i finally was able to fix chrome issue.  
It turned out when i created WebSocket connection in JS code - i specified sub-protocol string "binary" and the client didn't like response from the server and closed connection immediately.  
I tried to create WS without the sub-protocol string and magic, everything works!  
Sorry for false alarm.
