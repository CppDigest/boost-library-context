# #561 - Problems with Websocket handshakes [Closed]

> Username: ghost  
> Created at: 2017-06-30 14:41:14 UTC  
> Updated at: 2022-05-12 22:12:41 UTC  
> Closed at: 2017-08-16 00:27:59 UTC  
> Url: https://github.com/boostorg/beast/issues/561  

Hi, I'm having some problems getting the websocket connection working.  
  
I am using more or less the websocket client code of your example here:  
  
https://github.com/vinniefalco/Beast/blob/develop/example/websocket-client/websocket_client.cpp  
  
As a server I am using this one (Example 2):  
  
https://github.com/sta/websocket-sharp/tree/master/Example2  
  
The server receives the handshake but always fails with:  
  
                          HTTP/1.1 400 Bad Request  
                          Server: websocket-sharp/1.0  
                          Connection: close  
  
The boost/beast error message is: "WebSocket Upgrade handshake failed"  
  
Ip=XX.XXX.X.XX  
Port=4649  
  
Maybe I missed something obvious, but I expected the connection/handshake should be established between this client and server "out of the box"?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-30 14:47:48 UTC  
> Updated at: 2017-06-30 14:48:17 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-312287239  

>I expected the connection/handshake should be established between this client and server "out of the box"  
  
Yes, it should. We're going to need more information in order to resolve this. Could you please capture a network trace of a client that successfully connects to that server? I'd like to see both the request and the response. You can use a browser as your client, for example you can point this app to your local server on port 4649:  
https://www.websocket.org/echo.html  
  
Also just to make certain that the client is doing the right thing, could you put up a branch with the exact client code that you are using?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-01 22:36:41 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-312459282  

Is this still an issue?

---

## Comment 3

> Username: ghost  
> Created at: 2017-07-03 07:02:21 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-312565275  

Sorry, i couldn't test this at the weekend. Using the script from here:  
  
https://www.websocket.org/echo.html  
  
I also had no success connecting with the websocket-sharp server. So maybe the server implementation is broken. Here are the captured network pakets:  
  
Frame 185: 557 bytes on wire (4456 bits), 557 bytes captured (4456 bits) on interface 0  
Ethernet II, Src: IntelCor_08:79:5f (68:05:ca:08:79:5f), Dst: Fortinet_09:00:03 (00:09:0f:09:00:03)  
Internet Protocol Version 4, Src: XX.XX.XX.XX, Dst: XX.XXX.X.XX  
Transmission Control Protocol, Src Port: 49500, Dst Port: 4649, Seq: 1, Ack: 1, Len: 503  
Hypertext Transfer Protocol  
    GET / HTTP/1.1\r\n  
    Host: XX.XXX.X.XX:4649\r\n  
    User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:54.0) Gecko/20100101 Firefox/54.0\r\n  
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8\r\n  
    Accept-Language: de,en-US;q=0.7,en;q=0.3\r\n  
    Accept-Encoding: gzip, deflate\r\n  
    Sec-WebSocket-Version: 13\r\n  
    Origin: null\r\n  
    Sec-WebSocket-Extensions: permessage-deflate\r\n  
    Sec-WebSocket-Key: VgVzS1rryRTPVg5iIrKhGA==\r\n  
    Connection: keep-alive, Upgrade\r\n  
    Pragma: no-cache\r\n  
    Cache-Control: no-cache\r\n  
    Upgrade: websocket\r\n  
    \r\n  
    [Full request URI: http://XX.XXX.X.XX:4649/]  
    [HTTP request 1/1]  
    [Response in frame: 186]  
  
  
Frame 186: 134 bytes on wire (1072 bits), 134 bytes captured (1072 bits) on interface 0  
Ethernet II, Src: Fortinet_09:00:03 (00:09:0f:09:00:03), Dst: IntelCor_08:79:5f (68:05:ca:08:79:5f)  
Internet Protocol Version 4, Src: XX.XXX.X.XX, Dst: XX.XX.XX.XX  
Transmission Control Protocol, Src Port: 4649, Dst Port: 49500, Seq: 1, Ack: 504, Len: 80  
Hypertext Transfer Protocol  
    HTTP/1.1 501 Not Implemented\r\n  
    Server: websocket-sharp/1.0\r\n  
    Connection: close\r\n  
    \r\n  
    [HTTP response 1/1]  
    [Time since request: 0.009202000 seconds]  
    [Request in frame: 185]

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-07-03 08:33:29 UTC  
> Updated at: 2017-07-03 08:35:01 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-312583584  

Try connecting to `ws://X.X.X.X/Echo`  
  
replace `X.X.X.X` with the IP or domain name of the remote host running websocket-sharp  
  
If that works, then in Beast when you connect use `"/Echo"` for the `target` parameter in the call to handshake.

---

## Comment 5

> Username: ghost  
> Created at: 2017-07-03 09:01:10 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-312590025  

One additional question. Do I need to call the  
  
> io_service::run  
> http://www.boost.org/doc/libs/1_56_0/doc/html/boost_asio/reference/io_service/run/overload1.html  
  
function periodically using beast?  
And could you please provide some async websocket examples too?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-03 12:07:28 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-312628611  

You only need to call `run` if you use the asynchronous interfaces.  
  
Yes, I will add more examples :)

---

## Comment 7

> Username: ghost  
> Created at: 2017-07-07 07:39:28 UTC  
> Updated at: 2017-07-07 07:41:04 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-313610802  

I got some feedback of the hardware manufacturer. And you were right. I had to use an uri like "/x/y" for the websocket upgrade request / handshake. The code looks like this:  
  
> void SendUpgradeRequest()  
> 	{  
> 			boost::system::error_code errorCode;  
> 			webSocket.handshake_ex(ip, uri,  
> 				[](websocket::request_type& requestType)  
> 				{  
> 					requestType.set(http::field::user_agent, "My WebSocket");  
> 				}, errorCode);  
> 			if (errorCode)  
> 			{  
> 				LogError("Failed to send handshake: '%s'.", errorCode.message());  
> 			}  
> 		}  
  
The server is responding then with a "Switching protocols" answer as expected:  
  
> Hypertext Transfer Protocol  
>     HTTP/1.1 101 Switching Protocols\r\n  
>         [Expert Info (Chat/Sequence): HTTP/1.1 101 Switching Protocols\r\n]  
>             [HTTP/1.1 101 Switching Protocols\r\n]  
>             [Severity level: Chat]  
>             [Group: Sequence]  
>         Request Version: HTTP/1.1  
>         Status Code: 101  
>         Response Phrase: Switching Protocols  
>     Server: websocket-sharp/1.0\r\n  
>     Upgrade: websocket\r\n  
>     Connection: Upgrade\r\n  
>     Sec-WebSocket-Accept: EDqHu5tuHu00cmdsrKaOg4fk6is=\r\n  
>     \r\n  
>     [HTTP response 1/1]  
>     [Time since request: 0.004452000 seconds]  
>     [Request in frame: 51]  
  
Now I need to request ("GET") some data from the server by a second uri "/x/z".  
The following HTTP example worked for me:  
  
> bool SendRequest(const string& sUri)  
> 	{  
> 		boost::system::error_code errorCode;  
> 		http::request<http::string_body> req;  
> 		req.method(http::verb::get);  
> 		req.target(uri);  
> 		req.version = 11;  
> 		req.set(http::field::host, ip);  
> 		req.set(http::field::user_agent, "My WebSocket");  
> 		req.prepare_payload();  
> 		http::write(socket, req, errorCode);  
> 		if (errorCode == 0)  
> 		{  
> 			return true;  
> 		}  
> 		else  
> 		{  
> 			LogError("Failed to send data: '%s'.", errorCode.message());  
> 		}  
> 		return false;  
> 	}  
  
But how would I do this with the beast websocket?

---

## Comment 8

> Username: ghost  
> Created at: 2017-07-07 08:38:59 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-313623143  

And it seems that the websocket class does not handle ping/pongs on it's own? Do I need to handle them manually? I found no ping/pong example in the documentation and sadly all the links found via google or from older issues are broken.  
  
When the server sends a ping to the beast websocket client there is no response, so that the server always closes the connection.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-07-07 12:33:25 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-313668744  

_"During read operations, Beast automatically reads and processes control frames"_  
http://vinniefalco.github.io/beast/beast/using_websocket/control_frames.html  
  
You need to always have a read pending.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-07-07 12:34:46 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-313669033  

>Now I need to request ("GET") some data from the server by a second uri "/x/z".  
  
I don't understand what this means. GET is value found in HTTP requests. If you have to send an HTTP request then you need to use HTTP. WebSocket doesn't enter the picture. Just establish a second connection to your server and send the HTTP request as normal using Beast, then read the response.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-08-04 23:06:40 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-320371690  

Is this still an issue?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-08-16 00:27:59 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-322625087  

It looks like this issue has been resolved. If you are still have problems, please feel free to either re-open the issue or create a new one, thanks!

---

## Comment 13

> Username: Bardo91  
> Created at: 2022-02-14 18:17:41 UTC  
> Updated at: 2022-02-14 18:38:03 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1039404103  

I am facing exactly the same issue. I have a server in C# and for the client, I am using Boost Beast. I am using intensively boost beast in another application for the server too, and the connections within C++ work properly. The problem appeared when I started using a WebSocket server from another language. Particularly, I am testing the WebsocketSharp library. A minimal example is:  
- C#  https://gist.github.com/Bardo91/1fdbba1adb20a27976e72ee71794b6c3  
- C++  https://gist.github.com/Bardo91/82664c317e4926492c77d43be9223dff  
  
I have tried to connect to the C# server using python, and this webpage https://www.piesocket.com/websocket-tester and the connection works perfectly and I receive the messages.  
  
But when I use the sample code above in C++ the connection consistently fails to do the handshake. The exception captured says `Error: The WebSocket handshake was declined by the remote peer [boost.beast.websocket:20]`  
The deeper I can get is  in the "stream_iml.hpp" file, the response gives "Error 400" and fails there switching protocols  
![image](https://user-images.githubusercontent.com/3602335/153922140-8a2e1db4-c691-4b89-855f-6763e18d3150.png)  
  
I tried comparing the HTTP requests (from python or the webpage, and the beast one and seems identical).   
  
Can someone give me any clue about the issue? It seems something very simple, and probably the problem is a lack of knowledge of the protocol. But I cannot find any answer that suits my situation. Actually this is the closest one as it was raised using the same library on the server's side.   
  
BTW: I identified the error in Ubuntu 20.04, using boost 1.70. But It happens too in WIndows 11, using Boost 1.77 (installed using VCPKG).

---

## Comment 14

> Username: madmongo1  
> Created at: 2022-02-14 20:07:39 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1039504416  

Would you be in a position to share the request and response headers?

---

## Comment 15

> Username: Bardo91  
> Created at: 2022-02-14 20:26:57 UTC  
> Updated at: 2022-02-14 20:27:28 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1039523136  

For sure, this is the request I send using boost beast (which is filled automatically, I just set the User-Agent):  
```  
GET /test HTTP/1.1  
Host: localhost  
Upgrade: websocket  
Connection: upgrade  
Sec-WebSocket-Key: 3bvCeyC4WwSeBJ7TDYwgEQ==  
Sec-WebSocket-Version: 13  
User-Agent: Boost.Beast/322 websocket-client-coro  
```  
  
And the response is:  
```  
HTTP/1.1 400 Bad Request  
Server: websocket-sharp/1.0  
Connection: close  
```

---

## Comment 16

> Username: madmongo1  
> Created at: 2022-02-14 20:45:28 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1039539854  

Hmm it looks fine. What would the equivalent header look like if you used wscat or python?

---

## Comment 17

> Username: Bardo91  
> Created at: 2022-02-14 21:12:21 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1039564624  

This is what I get using python websocket-client module (https://pypi.org/project/websocket-client/)  
  
```  
--- request header ---  
GET /test HTTP/1.1  
Upgrade: websocket  
Host: 127.0.0.1:3000  
Origin: http://127.0.0.1:3000  
Sec-WebSocket-Key: C+/w6BD6XqbNxrJYbrB6+Q==  
Sec-WebSocket-Version: 13  
Connection: Upgrade  
  
  
--- response header ---  
HTTP/1.1 101 Switching Protocols  
Server: websocket-sharp/1.0  
Upgrade: websocket  
Connection: Upgrade  
Sec-WebSocket-Accept: gq+pGoN/PfY59rEoWvDNJYIRsUI=`  
```

---

## Comment 18

> Username: madmongo1  
> Created at: 2022-02-14 21:24:19 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1039577455  

There are two differences: the Host header has the port number and there is an Origin header.  
  
What’s happens if you set these fields prior to the async handshake?

---

## Comment 19

> Username: Bardo91  
> Created at: 2022-02-14 21:52:04 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1039605280  

That is true, actually, it is the first thing I tried to add yesterday unsuccessfully.  
  
**However, I have just tried now and it works, so probably I messed up with something yesterday.**  
  
As you have advised, I set these two fields and now the server is accepting the messages:  
```  
// Set a decorator to change the User-Agent of the handshake  
ws.set_option(websocket::stream_base::decorator(  
    [](websocket::request_type& req)  
    {  
        req.set(http::field::host, std::string("127.0.0.1:3000"));  
        req.set(http::field::origin, std::string("http://127.0.0.1:3000"));  
        std::cout << req << std::endl;  
    }));  
```  
  
So we can mark this issue as solved. Thank you very much. I hope this thread helps more people in the future.  
  
Just to conclude, is that a failure in the "basic" definition of an Upgrade request made with Beast, or is it an "extra requirement" that shouldn't be that strict by WebsocketSharp? From my perspective, it is a very simple example and both libraries should be compatible according to the standards. If the problem is in WebsocketSharp I might write to them so.  Thanks again.

---

## Comment 20

> Username: madmongo1  
> Created at: 2022-02-14 21:54:17 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1039606895  

I suspect it’s an in-necessary requirement of the c# library.  
I’d be interested in their answer.

---

## Comment 21

> Username: Bardo91  
> Created at: 2022-02-14 22:02:51 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1039613845  

Great, I will come back to this issue with their Response.

---

## Comment 22

> Username: Bardo91  
> Created at: 2022-02-17 17:32:24 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1043222059  

After reading more in depth the RFC6455 protocol (https://datatracker.ietf.org/doc/html/rfc6455#section-1.3), it seems that at least the header field "host" is required.  
  
```  
The client includes the hostname in the |Host| header field of its  
handshake as per [[RFC2616](https://datatracker.ietf.org/doc/html/rfc2616)], so that both the client and the server  
can verify that they agree on which host is in use.  
```  
  
The standard also says  
```  
Additional header fields are used to select options in the WebSocket  
Protocol.  Typical options available in this version are the  
subprotocol selector (|Sec-WebSocket-Protocol|), list of extensions  
support by the client (|Sec-WebSocket-Extensions|), **|Origin|** header  
field, etc.   
```  
  
So, shouldn't the implementation of Beast include at least the "host" by default?

---

## Comment 23

> Username: vinniefalco  
> Created at: 2022-02-17 19:46:39 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1043353948  

> shouldn't the implementation of Beast include at least the "host" by default?  
  
Maybe. The examples all set it though.

---

## Comment 24

> Username: Bardo91  
> Created at: 2022-02-17 20:56:07 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1043430384  

Actually, I followed the examples when I tried it first, and I think it is not added. For example, for the sync client example (https://www.boost.org/doc/libs/develop/libs/beast/example/websocket/client/sync/websocket_client_sync.cpp)  
  
The only header added in the `...set_option(decorator...)` is the "user-agent", but not the host field.

---

## Comment 25

> Username: madmongo1  
> Created at: 2022-02-17 21:40:24 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1043476017  

Hmm - I'll look into that - I wonder if we tested it against an undemanding host.

---

## Comment 26

> Username: kolomenkin  
> Created at: 2022-05-12 19:58:40 UTC  
> Updated at: 2022-05-12 19:59:46 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1125371387  

> Actually, I followed the examples when I tried it first, and I think it is not added. For example, for the sync client example (https://www.boost.org/doc/libs/develop/libs/beast/example/websocket/client/sync/websocket_client_sync.cpp)  
>   
> The only header added in the `...set_option(decorator...)` is the "user-agent", but not the host field.  
  
Those example from Boost:Beast does not work. Alas!  
The same problem as in the current topic (`[boost.beast.websocket:20]`)  
I mean it does not work with echo server "echo.websocket.org:80"

---

## Comment 27

> Username: sehe  
> Created at: 2022-05-12 20:08:14 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1125378958  

@kolomenkin It is unclear what "the current topic (`[boost.beast.websocket:20]`)" refers to. Can you edit to clarify/include a link?

---

## Comment 28

> Username: kolomenkin  
> Created at: 2022-05-12 20:24:54 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1125391337  

> boost.beast.websocket:20]  
  
I meant current issue (https://github.com/boostorg/beast/issues/561)  
  
You were referring Boost.Beast sample as working example. But those example is broken. It does not work and it is producing the same failure during handshake with `echo.websocket.org:80`. It cannot upgrade protocol too:  
`Error: The WebSocket handshake was declined by the remote peer [boost.beast.websocket:20]`  
  
My idea was we should not argue like "additional header is not added in example, so we don't need it too". Example does not work too.

---

## Comment 29

> Username: kolomenkin  
> Created at: 2022-05-12 20:35:17 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1125398880  

Sorry, it seems `echo.websocket.org` service is no longer available. I'm not sure how to test those sample correctly other way.

---

## Comment 30

> Username: vinniefalco  
> Created at: 2022-05-12 20:49:13 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1125409026  

Try these: https://blog.postman.com/introducing-postman-websocket-echo-service/

---

## Comment 31

> Username: kolomenkin  
> Created at: 2022-05-12 20:57:46 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1125415150  

They propose to use `wss://ws.postman-echo.com/raw`  
I modified example to have `/raw` at the end:  
`ws.handshake(host, "/raw");`  
  
And here is a result:  
  
```bash  
Debug>Client.exe ws.postman-echo.com 443 Helloo  
Error: The WebSocket handshake was declined by the remote peer [boost.beast.websocket:20]  
```  
  
Alas! :-(

---

## Comment 32

> Username: vinniefalco  
> Created at: 2022-05-12 21:23:05 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1125434765  

Maybe beast needs to add some more headers or something. Perhaps the Host field?

---

## Comment 33

> Username: kolomenkin  
> Created at: 2022-05-12 21:38:37 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1125444710  

Adding Host field does not help. I don't have any idea how to fix this.

---

## Comment 34

> Username: sehe  
> Created at: 2022-05-12 21:43:00 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1125447448  

Is SNI performed? What does the Host header require? Just `hostname.tld` or including port (like `hostname.tld:443`)?

---

## Comment 35

> Username: kolomenkin  
> Created at: 2022-05-12 22:12:07 UTC  
> Updated at: 2022-05-12 22:12:41 UTC  
> Url: https://github.com/boostorg/beast/issues/561#issuecomment-1125466792  

You were right about SNI & SSL.  
  
I was using example `beast/example/websocket/client/sync/websocket_client_sync.cpp` which does not work with most of WebSocket services I could find. All of them require SSL.  
  
And the following example works fine: `beast/example/websocket/client/sync-ssl/websocket_client_sync_ssl.cpp`  
  
Thanks a lot!
