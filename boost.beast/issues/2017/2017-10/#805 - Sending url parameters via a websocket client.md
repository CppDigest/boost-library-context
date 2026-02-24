# #805 - Sending url parameters via a websocket client? [Closed]

> Username: StuntHacks  
> Created at: 2017-10-03 17:20:25 UTC  
> Updated at: 2017-10-04 14:46:50 UTC  
> Closed at: 2017-10-04 14:46:50 UTC  
> Url: https://github.com/boostorg/beast/issues/805  

Hey,  
  
I am currently trying to create websocket connection to a server which expects some url parameters in order to accept the connection (wss://gateway.discord.gg/?v=6&encoding=json would be the final url).   
  
When using a websocket client (for example the Simple Websocket Client plugin for Firefox), the connection works and I immediatly get an hello message by the server. However, when trying to connect to it using beast, I alwas get the error "Error: stream truncated".  
  
The way I am trying to connect looks like this:  
  
```cpp  
	auto const host = argv[1];  
        auto const port = argv[2];  
        auto const text = argv[3];  
  
        // The io_service is required for all I/O  
        boost::asio::io_service ios;  
  
        // The SSL context is required, and holds certificates  
        ssl::context ctx{ssl::context::sslv23_client};  
  
        // This holds the root certificate used for verification  
        load_root_certificates(ctx);  
  
        // These objects perform our I/O  
        tcp::resolver resolver{ios};  
        websocket::stream<ssl::stream<tcp::socket>> ws{ios, ctx};  
  
        // Look up the domain name  
        auto const lookup = resolver.resolve({host, port});  
  
        // Make the connection on the IP address we get from a lookup  
        boost::asio::connect(ws.next_layer().next_layer(), lookup);  
  
        // Perform the SSL handshake  
        ws.next_layer().handshake(ssl::stream_base::client);  
  
        // Perform the websocket handshake  
        ws.handshake(host, "/?v=6&encoding=json");  
  
        // Send the message  
        ws.write(boost::asio::buffer(std::string(text)));  
  
        // This buffer will hold the incoming message  
        boost::beast::multi_buffer b;  
  
        // Read a message into our buffer  
        ws.read(b);  
  
        // Close the WebSocket connection  
        ws.close(websocket::close_code::normal);  
  
        // If we get here then the connection is closed gracefully  
  
        // The buffers() function helps print a ConstBufferSequence  
        std::cout << boost::beast::buffers(b.data()) << std::endl;  
```   
  
And the command I use to run it looks like this:  
  
`./test.out gateway.discord.gg 443 test`  
  
When using the same code in order to connect to `echo.websocket.org`, for example, it works, so it's not a problem with the connection.  
  
Here is the configuration I use for the websocket client plugin, in which it works:  
![screenshot](https://user-images.githubusercontent.com/11249519/31138711-ad4240ba-a86f-11e7-929a-2c0c3917936e.png)  
  
If I try running my code with the same payload, it doesn't work either. I know from playing around with the client that the server I am trying to connect to immediatly closes the connection if the two url parameters aren't present so I guess the problem lies somewhere there. I just can't figure out what it is.  
  
Thanks for any help!  
  
  
### Version of Beast  
  
120

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-03 17:31:35 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333919645  

So the call to `ws.handshake` fails?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-10-03 17:33:55 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333920231  

Hmm.... that `load_root_certificates`  just loads a few canned CA certificates into the verify context. It is very likely that it doesn't have a CA for the certificate presented by your Discord server.  
  
Simple Websocket Client for Firefox has a bunch of code to access the operating-system-specific certificate store to perform the verification but Beast doesn't have that. However, perhaps certificates are not your problem, is the call to `ws.next_layer().handshake()` proceeding without error?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-03 17:38:24 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333921497  

If you can capture the serialized representation of the outgoing HTTP Upgrade request coming from Simple Websocket Client, then we can compare it to the Beast output. I would try doing that.

---

## Comment 4

> Username: StuntHacks  
> Created at: 2017-10-03 18:17:56 UTC  
> Updated at: 2017-10-03 18:18:28 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333932802  

Thanks for your quick respons!  
  
As far as I can see, the call to `ws.next_layer().handshake()` doesn't produce any error, however I am not quite sure how to test this.  
  
I am now trying to get the outgoing request, but that will probably be a bit tricky. Fortunately, it is just a simple page that uses js for the socket so it should be possible I think.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-10-03 18:19:59 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333933352  

Put a print statement after the call to `ws.next_layer().handshake()` and see if it executes. Make sure to flush the stream (write `std::endl`).  
  
As for the serialized request, if you go into debugging mode in your browser it should be in there.

---

## Comment 6

> Username: StuntHacks  
> Created at: 2017-10-03 18:25:47 UTC  
> Updated at: 2017-10-03 19:22:17 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333935075  

Okay, so here are the request and response headers:  
**Request:**  
```  
Host: gateway.discord.gg  
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:55.0) Gecko/20100101 Firefox/55.0  
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8  
Accept-Language: en-US,en;q=0.5  
Accept-Encoding: gzip, deflate, br  
Sec-WebSocket-Version: 13  
Origin: moz-extension://0594ca87-4f91-41e2-8505-59a8f12a58c3  
Sec-WebSocket-Extensions: permessage-deflate  
Sec-WebSocket-Key: R8tOKWKRuDtGNnyFHhSCwQ==  
Cookie: __cfduid=xxxxxxxxxxxxxxx  
Connection: keep-alive, Upgrade  
Pragma: no-cache  
Cache-Control: no-cache  
Upgrade: websocket  
```  
**Response:**  
```  
Date: Tue, 03 Oct 2017 18:18:58 GMT  
Connection: upgrade  
upgrade: websocket  
sec-websocket-accept: qnUR2/RbMzzEFin0kGU80sH50bE=  
Server: cloudflare-nginx  
CF-RAY: 3a81e8353e5d5954-VIE  
```  
  
I tried the code now and it works up until `ws.read()`, that's where the exception gets thrown.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-10-03 18:28:05 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333935724  

1. Where is the **request-line**? i.e. the part with "GET /?..."?  
  
2. What does Beast's request look like?

---

## Comment 8

> Username: StuntHacks  
> Created at: 2017-10-03 18:33:30 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333937286  

Oh, I just noticed firefox didn't write the request line into the raw output. However, it kinda pretty prints the specific parts, so it's possible to create it from that:  
**Request URL:** https://gateway.discord.gg/?v=6&encoding=json  
**Request method:** GET  
**Version:** HTTP/1.1  
  
Sorry if this is a dumb question but how would I go about getting the request from Beast?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-10-03 18:37:38 UTC  
> Updated at: 2017-10-03 18:38:02 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333938508  

>Sorry if this is a dumb question but how would I go about getting the request from Beast?  
  
Not a dumb question. The easiest way is to modify the beast source code to just print it to `cerr` or `cout`. For `async_handshake`, add a line in the constructor for `data` to print `req`:  
https://github.com/boostorg/beast/blob/7fe74b1bf544a64ecf8985fde44abf88f9902251/include/boost/beast/websocket/impl/handshake.ipp#L60  
```  
std::cout << req << std::endl;  
```  
  
For `handshake` do it here:  
https://github.com/boostorg/beast/blob/7fe74b1bf544a64ecf8985fde44abf88f9902251/include/boost/beast/websocket/impl/handshake.ipp#L398  
  
`req` contains the outgoing request.

---

## Comment 10

> Username: StuntHacks  
> Created at: 2017-10-03 18:47:37 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333941328  

The request sent by boost looks like this:  
```  
GET /?v=6&encoding=json HTTP/1.1  
Host: gateway.discord.gg  
Upgrade: websocket  
Connection: upgrade  
Sec-WebSocket-Key: BFRBAo7hMDz9nvIEnKJIcA==  
Sec-WebSocket-Version: 13  
User-Agent: Boost.Beast/120  
```

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-10-03 18:50:37 UTC  
> Updated at: 2017-10-03 18:50:56 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333942144  

Maybe you need that cookie (`__cfduid`)?  
  
Maybe the server wants "Connection: Upgrade" (case-sensitive?) You can change it here:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/impl/stream.ipp#L530

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-10-03 18:52:10 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333942540  

You can try adding some of those other fields, like "Origin", by calling `handshake_ex` which takes a decorator, and inserting the fields:  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/handshake_ex/overload1.html

---

## Comment 13

> Username: StuntHacks  
> Created at: 2017-10-03 19:05:21 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333946089  

Okay so I asked some people who work with the api and they suggested trying to change the request line in order to contain the url als well (so from `GET /?v=6&encoding=json HTTP/1.1` to `GET https://gateway.discord.gg/?v=6&encoding=json HTTP/1.1`, like in the request made by firefox). Is there any way of doing this?

---

## Comment 14

> Username: StuntHacks  
> Created at: 2017-10-03 19:50:55 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333957962  

Update: I just tried to make the request using curl and manually setting each header field to the same as in the beast request and it worked. The only thing different is the request line, which uses the full url (because curl doesn't work when only given the parameters).

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-10-03 20:02:05 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333961121  

>Is there any way of doing this?  
  
Yes:  
```  
req.target("https://gateway.discord.gg/?v=6&encoding=json");  
```  
  
However, the GET method does not expect an absolute-uri. Do you have a proxy configured for your operating system? Is your browser using a proxy?

---

## Comment 16

> Username: StuntHacks  
> Created at: 2017-10-03 20:28:35 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333968295  

No, neither my os nor my browser are using a proxy.  
  
If it's not the request line, it's probably the ca certificates? I asked about what certificates the server uses and I was told that they are present on every modern os (as you said, the reason why curl and simple websocket client work).  
  
Since beast doesn't have any functionality to load the certificates directly from the os, would it be possible to just replace the ones in the `root_certificates.hpp` with any certificates from the os?

---

## Comment 17

> Username: vinniefalco  
> Created at: 2017-10-03 20:30:49 UTC  
> Updated at: 2017-10-03 20:31:38 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333968893  

I would first try modifying the request line and see if that helps. Otherwise, you have to load the certificates from the OS and that is going to be kind of tricky. Here is some code you might use:  
https://github.com/Microsoft/cpprestsdk/blob/master/Release/src/http/client/x509_cert_utilities.cpp  
  
Although, I don't think your problem is certificates because you aren't setting the "verify_peer" option in the ssl context. So it must be something else.

---

## Comment 18

> Username: StuntHacks  
> Created at: 2017-10-03 20:40:19 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333971462  

When modifying the request line, I get a WebSocket upgrade handshake failed error.  
  
So I will probably have to take a look at that certificate code...

---

## Comment 19

> Username: vinniefalco  
> Created at: 2017-10-03 20:58:32 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-333976471  

I think someone had a similar problem with SSL streams and it was caused by the server not doing reads properly, you might want to sift through the closed issues.

---

## Comment 20

> Username: StuntHacks  
> Created at: 2017-10-04 06:28:21 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-334061618  

Okay, so I managed to hack a little solution together using boost so it works now, I will probably have to refactor it in the future though.  
  
Anyway, thanks for your quick and competent help, it's not often the case that one gets an answer to an issue within literally less then half of an hour.

---

## Comment 21

> Username: vinniefalco  
> Created at: 2017-10-04 14:19:19 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-334170767  

What was the problem? Can you post the boost code?

---

## Comment 22

> Username: StuntHacks  
> Created at: 2017-10-04 14:30:32 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-334174589  

Well, I actually don't know what exactly solved it in the end. I guess it was that I used an outdated version because I just updated to the latest dev release (I was on, I don't know, 1.38 or something like that maybe?) and to OpenSSL 1.1.0 and now it worked, for whatever reason. Right now I am using the exact same code as the last time I tried it now.  
  
Before updating, I tried stitching together a solution using [this](https://stackoverflow.com/questions/28264313/ssl-certificates-and-boost-asio) stackoverflow answer. However, that didn't work on some servers for some reason.

---

## Comment 23

> Username: vinniefalco  
> Created at: 2017-10-04 14:33:22 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-334175530  

Are you telling me that you're still using Beast code, and everything is working just by updating Boost and OpenSSL?

---

## Comment 24

> Username: StuntHacks  
> Created at: 2017-10-04 14:40:48 UTC  
> Updated at: 2017-10-04 14:45:00 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-334177908  

~~At least I think I am. I will look at it again when I'm at home but I can't remember that the code is different...~~  
  
EDIT: Okay nevermind. I was curious so I decided to look into it again and I completely forgot that I replaced one of the certificate in the `root_certificates.h` with one pre-installed on the os. My bad and sorry for the confusion.

---

## Comment 25

> Username: vinniefalco  
> Created at: 2017-10-04 14:46:00 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-334179567  

Okay, that's good to know! It sounds like there's no problem with beast. Yes I agree that certificates are a pain in the ass. I'm working on a new project to solve this problem.

---

## Comment 26

> Username: StuntHacks  
> Created at: 2017-10-04 14:46:50 UTC  
> Url: https://github.com/boostorg/beast/issues/805#issuecomment-334179862  

Awesome. Thanks for your patient help then, I think this can be closed now! :)
