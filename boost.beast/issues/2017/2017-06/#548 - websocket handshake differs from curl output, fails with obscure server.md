# #548 - websocket handshake differs from curl output, fails with obscure server [Closed]

> Username: larsonmpdx  
> Created at: 2017-06-27 17:24:46 UTC  
> Updated at: 2017-09-01 18:36:32 UTC  
> Closed at: 2017-07-04 01:54:08 UTC  
> Url: https://github.com/boostorg/beast/issues/548  

beast version 67  
  
note I have replaced the host with [host] throughout, I can privately share the real host to help testing  
  
I'm using this curl command to handshake with a server based on a pascal websocket server (http://www.esegece.com/websockets/)  
  
```  
curl -v -i -N -H "Connection: Upgrade" -H "Upgrade: websocket" -H "Host: [host]" -H "Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==" -H "Sec-WebSocket-Version: 13" https://[host]  
```  
  
I am trying to do the same handshake with beast, even copying the curl headers:  
  
```  
        beast::websocket::response_type res;  
        ws.handshake_ex(res, host, "/",  
                        [](beast::websocket::request_type &m)  
                        {  
                            m.insert(beast::http::field::accept, "*/*");  
                            m.insert(beast::http::field::user_agent, "curl/7.47.0");  
                            std::cout << m << std::endl;  
                        },  
                        ec);  
  
        std::cout << res << std::endl;  
```  
  
the server accepts the curl connection but rejects the beast connection.  the server's logs show that the headers are received with extra newlines or something like newlines in the case of beast:  
  
server logs for curl connection:  
```  
Recv: GET / HTTP/1.1  
Host: [host]  
User-Agent: curl/7.47.0  
Accept: */*  
Connection: Upgrade  
Upgrade: websocket  
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==  
Sec-WebSocket-Version: 13  
  
Sent: HTTP/1.1 101 Switching Protocols  
Upgrade: websocket  
Connection: Upgrade  
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=  
Server: sgcWebSockets 3.5  
```  
  
server logs for beast:  
```  
Recv: GET  
Recv:  /  
Recv:  HTTP/1.1  
  
Recv: Host: [host]  
  
Recv: Upgrade: websocket  
  
Recv: Connection: upgrade  
  
Recv: Sec-WebSocket-Key: lD7oPd7/ryIbKDcgimpDAA==  
  
Recv: Sec-WebSocket-Version: 13  
  
Recv: Accept: */*  
  
Recv: User-Agent: curl/7.47.0  
  
Recv:   
  
Sent: HTTP/1.1 302 Moved Temporarily  
Connection: close  
Content-Length: 0  
Date: Tue, 27 Jun 2017 17:01:11 GMT  
Server: sgcWebSockets 3.5  
Location: login  
Set-Cookie: IDHTTPSESSIONID=ANv2nTA6vZ8PuVx; Path=/  
```  
  
I also tested beast using mitmproxy (https://mitmproxy.org/) instead of connecting to the server directly, and it works correctly in this case, as I assume mitmproxy is putting the headers together.  I assume my problem has to do with the extra newlines in the beast output.  This may be standards-compliant (I don't know), but it's making my server unhappy.  Is there a way to change this?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-27 17:44:49 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311433125  

Hmm, I don't think there are extra newlines in the Beast output. cUrl has them as well, you just don't see it because they filter it from the log (to avoid the double line spacing problem with Beast's output).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-27 17:46:11 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311433500  

Is http://www.esegece.com/websockets/ open source? Perhaps you could contact them and ask if their implementation correctly parses HTTP requests with the RFC-specified CRLF endings (`"\r\n"`)

---

## Comment 3

> Username: larsonmpdx  
> Created at: 2017-06-27 17:59:25 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311437025  

the server looks closed source, it's also kind of a black box to me as I don't operate it, I can just see log output  
  
I think there is a difference in line endings between curl and beast, the server is just printing things as they arrive and is printing extra newlines, those must come from somewhere.  Maybe it doesn't matter except for this:  
```  
Recv: GET  
Recv:  /  
Recv:  HTTP/1.1  
```  
curl has this on one line

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-27 18:03:28 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311438140  

That's a clue. What operating system, version of Boost, and compiler are you using to build your application?

---

## Comment 5

> Username: larsonmpdx  
> Created at: 2017-06-27 18:08:50 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311439569  

ubuntu 16.04, boost 1.62.0 and gcc 5.4.0.  I'm using this trick to use boost system as header-only #231

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-27 18:21:01 UTC  
> Updated at: 2017-06-27 18:21:19 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311442735  

Its a very funny (or not so funny) coincidence that the serializer for `basic_fields` happens to chop up the request line into 3 pieces. Lines 179. 180, and 181 are for the request line pieces:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/fields.ipp#L178

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-06-27 18:35:19 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311446614  

I don't think this is extra newlines:  
```  
Recv: GET  
Recv:  /  
Recv:  HTTP/1.1  
  
Recv: Host: [host]  
  
Recv: Upgrade: websocket  
```  
  
Is "Recv: ..." output coming from the sgcWebSockets? I think what's happening is that the server is receiving the header in multiple "chops" (TCP/IP packets) and it just happens to print a a newline when it sees "\r\n". So here, it got 3 chops with the last chop ending in "\r\n":  
```  
Recv: GET  
Recv:  /  
Recv:  HTTP/1.1  
.  
```  
  
And here it got one chop which ended in a newline:  
```  
Recv: Upgrade: websocket  
  
```  
  
Both cUrl and Beast are sending correct HTTP WebSocket Upgrade requests, the difference is that cUrl is sending it in one buffer while Beast is sending it with several. There are two important questions we need to answer:  
  
1. Beast presents a vector of buffers to a single Asio `write` operation, so why are they going out fragmented? and  
  
2. Why doesn't sgcWebSockets server handle the case where an incoming upgrade request is fragmented?

---

## Comment 8

> Username: larsonmpdx  
> Created at: 2017-06-27 18:47:22 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311449831  

I think your suspicion that sgcWebSockets is somehow not standards-compliant is probably correct.  My next step will be to look at the packets, I haven't done that yet because my test server has ssl enabled.  I can see that both curl and beast are sending a single ssl tcp packet for the request.  The beast packet is slightly bigger which makes me guess that it has extra newline chars.  I'll capture some unencrypted packets and report back

---

## Comment 9

> Username: larsonmpdx  
> Created at: 2017-06-27 18:47:52 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311449969  

yes all output is from the server logs

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-06-27 18:49:14 UTC  
> Updated at: 2017-06-27 18:49:27 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311450340  

>The beast packet is slightly bigger which makes me guess that it has extra newline chars  
  
That doesn't make sense because Beast serializes HTTP messages correctly. If it didn't, the tests would fail. This is quite a mystery!

---

## Comment 11

> Username: larsonmpdx  
> Created at: 2017-06-27 18:53:05 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311451350  

right I am not suggesting beast is not standards compliant, just that there is some small difference from curl that is bringing out a problem with this server

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-06-27 18:55:21 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311452022  

cUrl must be standards compliant as well, if it wasn't - we'd know!!

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-06-27 18:59:26 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311453135  

Try replacing this line:  
```  
m.insert(beast::http::field::user_agent, "curl/7.47.0");  
```  
with  
```  
m.set(beast::http::field::user_agent, BEAST_VERSION);  
```  
  
Maybe the server is doing a special-case for cUrl?

---

## Comment 14

> Username: larsonmpdx  
> Created at: 2017-06-27 19:11:03 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311456090  

no change with the different user agent against sgcWebSockets  
  
here are the request packets from curl (top) and beast (bottom) against echo.websocket.org.  The headers themselves look identical, no extra newlines or anything  
  
the commands to generate this are:  
`curl -v -i -N -H "Connection: Upgrade" -H "Upgrade: websocket" -H "Host: echo.websocket.org" -H "Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==" -H "Sec-WebSocket-Version: 13" http://echo.websocket.org`  
and  
`sudo tcpdump -nnvXSs 0 "src host [your host] and dst port 80"`  
plus the beast websocket client example  
  
```  
	GET / HTTP/1.1  
	Host: echo.websocket.org  
	User-Agent: curl/7.47.0  
	Accept: */*  
	Connection: Upgrade  
	Upgrade: websocket  
	Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==  
	Sec-WebSocket-Version: 13  
	  
	0x0000:  4500 00f7 4c59 4000 4006 9ccc c0a8 0168  E...LY@.@......h  
	0x0010:  ae81 e049 b0be 0050 0994 9902 5f3b 171b  ...I...P...._;..  
	0x0020:  8018 00e5 51c5 0000 0101 080a 012d d56e  ....Q........-.n  
	0x0030:  0620 38e1 4745 5420 2f20 4854 5450 2f31  ..8.GET./.HTTP/1  
	0x0040:  2e31 0d0a 486f 7374 3a20 6563 686f 2e77  .1..Host:.echo.w  
	0x0050:  6562 736f 636b 6574 2e6f 7267 0d0a 5573  ebsocket.org..Us  
	0x0060:  6572 2d41 6765 6e74 3a20 6375 726c 2f37  er-Agent:.curl/7  
	0x0070:  2e34 372e 300d 0a41 6363 6570 743a 202a  .47.0..Accept:.*  
	0x0080:  2f2a 0d0a 436f 6e6e 6563 7469 6f6e 3a20  /*..Connection:.  
	0x0090:  5570 6772 6164 650d 0a55 7067 7261 6465  Upgrade..Upgrade  
	0x00a0:  3a20 7765 6273 6f63 6b65 740d 0a53 6563  :.websocket..Sec  
	0x00b0:  2d57 6562 536f 636b 6574 2d4b 6579 3a20  -WebSocket-Key:.  
	0x00c0:  6447 686c 4948 4e68 6258 4273 5a53 4275  dGhlIHNhbXBsZSBu  
	0x00d0:  6232 356a 5a51 3d3d 0d0a 5365 632d 5765  b25jZQ==..Sec-We  
	0x00e0:  6253 6f63 6b65 742d 5665 7273 696f 6e3a  bSocket-Version:  
	0x00f0:  2031 330d 0a0d 0a                        .13....  
  
  
  
  
  
	GET / HTTP/1.1  
	Host: echo.websocket.org  
	Upgrade: websocket  
	Connection: upgrade  
	Sec-WebSocket-Key: x2euBA4jojHvJVBLciOhbQ==  
	Sec-WebSocket-Version: 13  
	Accept: */*  
	User-Agent: 67  
	  
	0x0000:  4500 00ee a1ee 4000 4006 4740 c0a8 0168  E.....@.@.G@...h  
	0x0010:  ae81 e049 b0cc 0050 8313 e579 af83 859a  ...I...P...y....  
	0x0020:  8018 00e5 51bc 0000 0101 080a 0130 45a5  ....Q........0E.  
	0x0030:  0622 a822 4745 5420 2f20 4854 5450 2f31  ."."GET./.HTTP/1  
	0x0040:  2e31 0d0a 486f 7374 3a20 6563 686f 2e77  .1..Host:.echo.w  
	0x0050:  6562 736f 636b 6574 2e6f 7267 0d0a 5570  ebsocket.org..Up  
	0x0060:  6772 6164 653a 2077 6562 736f 636b 6574  grade:.websocket  
	0x0070:  0d0a 436f 6e6e 6563 7469 6f6e 3a20 7570  ..Connection:.up  
	0x0080:  6772 6164 650d 0a53 6563 2d57 6562 536f  grade..Sec-WebSo  
	0x0090:  636b 6574 2d4b 6579 3a20 7832 6575 4241  cket-Key:.x2euBA  
	0x00a0:  346a 6f6a 4876 4a56 424c 6369 4f68 6251  4jojHvJVBLciOhbQ  
	0x00b0:  3d3d 0d0a 5365 632d 5765 6253 6f63 6b65  ==..Sec-WebSocke  
	0x00c0:  742d 5665 7273 696f 6e3a 2031 330d 0a41  t-Version:.13..A  
	0x00d0:  6363 6570 743a 202a 2f2a 0d0a 5573 6572  ccept:.*/*..User  
	0x00e0:  2d41 6765 6e74 3a20 3637 0d0a 0d0a       -Agent:.67....  
```

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-06-27 19:18:58 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311457976  

Looks like cUrl is sending Upgrade capitalized:  
```  
Connection: Upgrade  
```  
  
Can you try editing your `beast/websocket/impl/stream.ipp` file and changing `"upgrade"` to `"Upgrade"` please? Here:  
https://github.com/vinniefalco/Beast/blob/4269f354e8c7142a1015e1efe314d851fc5d3f86/include/beast/websocket/impl/stream.ipp#L170

---

## Comment 16

> Username: larsonmpdx  
> Created at: 2017-06-27 19:20:53 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311458426  

oh, sorry, yes I have tried both ways and there is no change, that was my mistake making them different here

---

## Comment 17

> Username: vinniefalco  
> Created at: 2017-06-27 19:21:21 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311458540  

You tried editing Beast source code?

---

## Comment 18

> Username: vinniefalco  
> Created at: 2017-06-27 19:31:21 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311461042  

I have no idea why this is happening, nor any way to reproduce it since I don't have access to the server. I don't know what to do here. I have contacted the company that produces sgcWebSockets and directed them to the issue. What I don't understand is why, if cUrl and Beast are both delivering the HTTP Upgrade in one TCP packet, and they are both producing the same or equivalent HTTP requests, then why does one work and the other not? And why is the server showing Beast's request chopped up? Its not because of newlines. But the chops happen to exactly coincide with how Beast assembles a buffer sequence to represent the header:  
https://github.com/vinniefalco/Beast/blob/4269f354e8c7142a1015e1efe314d851fc5d3f86/include/beast/http/impl/fields.ipp#L149  
```  
    using const_buffers_type =  
        buffer_cat_view<  
            boost::asio::const_buffers_1, // <-- This is the "GET"  
            boost::asio::const_buffers_1, // <-- This is the " /" (note, space precedes the text)  
            boost::asio::const_buffers_1, // <-- This is the " HTTP/1.1\r\n" (note, space precedes the text)  
            field_range,                  // <-- This is a series of fields  
            boost::asio::const_buffers_1>;// <-- This is the final "\r\n"  
```  
  
Each field is represented by one buffer in the `field_range` including the colon, space, and CRLF, for example:  
```  
"Connection: upgrade\r\n" // <-- one element of the field_range  
```  
  
So the number of buffers in the buffer sequence that Beast writes to the socket is `3+N+1` where `N` is the number of fields. Back to your output:  
```  
Recv: GET  
Recv:  /  
Recv:  HTTP/1.1  
  
Recv: Host: [host]  
  
Recv: Upgrade: websocket  
  
Recv: Connection: upgrade  
  
Recv: Sec-WebSocket-Key: lD7oPd7/ryIbKDcgimpDAA==  
  
Recv: Sec-WebSocket-Version: 13  
  
Recv: Accept: */*  
  
Recv: User-Agent: curl/7.47.0  
  
Recv:   
  
```  
  
There are exactly 11 "Recv" lines, and 7 fields. 3 + 7 + 1 == 11. Somehow the server is seeing each individual buffer in Beast's buffer sequence.  
  
You can try the following:  
```  
using namespace beast;  
multi_buffer b;  
ostream(b) <<  
    "GET / HTTP/1.1\r\n"  
    "Host: [host]\r\n"  
    "User-Agent: Beast\r\n"  
    "Accept: */*\r\n"  
    "Connection: Upgrade\r\n"  
    "Upgrade: websocket\r\n"  
    "Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==\r\n"  
    "Sec-WebSocket-Version: 13\r\n"  
    "\r\n"  
boost::asio::write(sock, b.data());  
```  
This will manually send a handshake. Confirm that this works, by looking at the server's output. Then try this:  
```  
using namespace beast;  
multi_buffer b1, b2;  
ostream(b1) <<  
    "GET / HTTP/1.1\r\n"  
    "Host: [host]\r\n"  
    "User-Agent: Beast\r\n"  
    "Accept: */*\r\n";  
ostream(b2) <<  
    "Connection: Upgrade\r\n"  
    "Upgrade: websocket\r\n"  
    "Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==\r\n"  
    "Sec-WebSocket-Version: 13\r\n"  
    "\r\n"  
boost::asio::write(sock, buffer_cat(b1.data(), b2.data()));  
```  
Now see what happens at the server's end.

---

## Comment 19

> Username: larsonmpdx  
> Created at: 2017-06-27 19:51:00 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311465996  

> You tried editing Beast source code?  
  
I changed the curl test to lowercase to match and that still worked with sgcWebSockets :) I did a test with "Upgrade" in beast's source code and there was no change  
  
the sgcWebSockets test server uses ssl and I'm stumped at trying to pass a multi_buffer to the stream without using a ws.write() method, can you give some example?

---

## Comment 20

> Username: vinniefalco  
> Created at: 2017-06-27 19:58:51 UTC  
> Updated at: 2017-06-27 19:59:23 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311467980  

Don't use `beast::websocket::stream`, use a raw `boost::asio::ssl::stream<boost::asio::ip::tcp::socket>`. There's source code for doing that here:  
https://github.com/vinniefalco/Beast/blob/master/example/http-client-ssl/http_client_ssl.cpp#L57  
  
You want everything from lines 23 through 57, change the host and protocol accordingly (or use a manual port like `"443"`). Pass the `stream` to `boost::asio::write`. Sorry about this. But it will help us get to the bottom of it!

---

## Comment 21

> Username: larsonmpdx  
> Created at: 2017-06-27 20:01:51 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311468744  

no problem at all, I will get to this in a couple hours.  Thanks for all the help

---

## Comment 22

> Username: vinniefalco  
> Created at: 2017-06-27 20:14:37 UTC  
> Updated at: 2017-06-27 20:14:48 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311471961  

FYI:  
  
  
Hello,  
  
Yes it handles header lines and is full compliant with RFC6455. You can see compliant tests from:  
  
http://www.esegece.com/reports/servers/  
  
Kind Regards,  
Sergio  
  
2017-06-27 19:55 GMT+02:00 eSeGeCe <info@esegece.com>:  
This is an enquiry email via http://www.esegece.com/ from:  
Vinnie Falco <vinnie.falco@gmail.com>  
  
Does your websocket server correctly handle `"\r\n"` at the end of lines in the HTTP header? Check this out:  
https://github.com/vinniefalco/Beast/issues/548

---

## Comment 23

> Username: larsonmpdx  
> Created at: 2017-06-28 00:02:10 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311519174  

The first case does get a proper response from sgcWebSockets.  On the second test I get newline output right where the split is between the two buffer_cat() parts.  Here is tcpdump output comparing the packets (when sent to echo.websocket.org - [host] seems to not matter so I didn't bother fixing it)  
  
with buffer_cat() split  
```  
	GET / HTTP/1.1  
	Host: [host]  
	User-Agent: Beast  
	Accept: */*  
	Connection: Upgrade  
	Upgrade: websocket  
	Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==  
	Sec-WebSocket-Version: 13  
	  
	0x0000:  4500 00e5 4b50 4000 4006 9de7 c0a8 0168  E...KP@.@......h  
	0x0010:  ae81 e049 b150 0050 96f4 57b7 6674 6ca8  ...I.P.P..W.ftl.  
	0x0020:  8018 00e5 51b3 0000 0101 080a 0171 1d10  ....Q........q..  
	0x0030:  0663 80e4 4745 5420 2f20 4854 5450 2f31  .c..GET./.HTTP/1  
	0x0040:  2e31 0d0a 486f 7374 3a20 5b68 6f73 745d  .1..Host:.[host]  
	0x0050:  0d0a 5573 6572 2d41 6765 6e74 3a20 4265  ..User-Agent:.Be  
	0x0060:  6173 740d 0a41 6363 6570 743a 202a 2f2a  ast..Accept:.*/*  
	0x0070:  0d0a 436f 6e6e 6563 7469 6f6e 3a20 5570  ..Connection:.Up  
	0x0080:  6772 6164 650d 0a55 7067 7261 6465 3a20  grade..Upgrade:.  
	0x0090:  7765 6273 6f63 6b65 740d 0a53 6563 2d57  websocket..Sec-W  
	0x00a0:  6562 536f 636b 6574 2d4b 6579 3a20 6447  ebSocket-Key:.dG  
	0x00b0:  686c 4948 4e68 6258 4273 5a53 4275 6232  hlIHNhbXBsZSBub2  
	0x00c0:  356a 5a51 3d3d 0d0a 5365 632d 5765 6253  5jZQ==..Sec-WebS  
	0x00d0:  6f63 6b65 742d 5665 7273 696f 6e3a 2031  ocket-Version:.1  
	0x00e0:  330d 0a0d 0a                             3....  
```  
without split  
```  
	GET / HTTP/1.1  
	Host: [host]  
	User-Agent: Beast  
	Accept: */*  
	Connection: Upgrade  
	Upgrade: websocket  
	Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==  
	Sec-WebSocket-Version: 13  
	  
	0x0000:  4500 00e5 fe15 4000 4006 eb21 c0a8 0168  E.....@.@..!...h  
	0x0010:  ae81 e049 b15a 0050 ad7d a98a 7d91 31ac  ...I.Z.P.}..}.1.  
	0x0020:  8018 00e5 51b3 0000 0101 080a 0172 57b2  ....Q........rW.  
	0x0030:  0664 bb87 4745 5420 2f20 4854 5450 2f31  .d..GET./.HTTP/1  
	0x0040:  2e31 0d0a 486f 7374 3a20 5b68 6f73 745d  .1..Host:.[host]  
	0x0050:  0d0a 5573 6572 2d41 6765 6e74 3a20 4265  ..User-Agent:.Be  
	0x0060:  6173 740d 0a41 6363 6570 743a 202a 2f2a  ast..Accept:.*/*  
	0x0070:  0d0a 436f 6e6e 6563 7469 6f6e 3a20 5570  ..Connection:.Up  
	0x0080:  6772 6164 650d 0a55 7067 7261 6465 3a20  grade..Upgrade:.  
	0x0090:  7765 6273 6f63 6b65 740d 0a53 6563 2d57  websocket..Sec-W  
	0x00a0:  6562 536f 636b 6574 2d4b 6579 3a20 6447  ebSocket-Key:.dG  
	0x00b0:  686c 4948 4e68 6258 4273 5a53 4275 6232  hlIHNhbXBsZSBub2  
	0x00c0:  356a 5a51 3d3d 0d0a 5365 632d 5765 6253  5jZQ==..Sec-WebS  
	0x00d0:  6f63 6b65 742d 5665 7273 696f 6e3a 2031  ocket-Version:.1  
	0x00e0:  330d 0a0d 0a                             3....  
```  
  
these look identical except for the beginning, I haven't tried to decode that

---

## Comment 24

> Username: vinniefalco  
> Created at: 2017-06-28 00:03:47 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311519393  

>On the second test I get newline output right where the split is between the two buffer_cat() parts  
  
Can you show the log?

---

## Comment 25

> Username: larsonmpdx  
> Created at: 2017-06-28 00:08:51 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311520190  

```  
Recv: GET / HTTP/1.1  
Host: [host]  
User-Agent: Beast  
Accept: */*  
  
Recv: Connection: Upgrade  
Upgrade: websocket  
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==  
Sec-WebSocket-Version: 13  
  
  
Sent: HTTP/1.1 302 Moved Temporarily  
Connection: close  
Content-Length: 0  
Date: Tue, 27 Jun 2017 23:29:51 GMT  
Server: sgcWebSockets 3.5  
Location: login  
Set-Cookie: IDHTTPSESSIONID=xsenCjaLK96NzNV; Path=/  
```

---

## Comment 26

> Username: vinniefalco  
> Created at: 2017-06-28 00:08:53 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311520199  

The only thing left is to step into `boost::asio::write` and figure out why its not sending both buffers at once.  
  
The other problem is that this server..doesn't seem to handle fragmented messages (more than one piece).

---

## Comment 27

> Username: vinniefalco  
> Created at: 2017-06-28 00:09:33 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311520308  

>On the second test I get newline output right where the split is between the two buffer_cat() parts.   
  
Remember that's just a newline generated by their logging code. The newline is not present on the wire, as your packet trace indicates.

---

## Comment 28

> Username: larsonmpdx  
> Created at: 2017-06-28 00:45:40 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311525138  

yes beast seems to be working correctly aside from any efficiency lost to the packet splitting.  I will bug the sgcWebSockets guys.  thanks for looking at this

---

## Comment 29

> Username: vinniefalco  
> Created at: 2017-06-28 01:02:57 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311527581  

>yes beast seems to be working correctly aside from any efficiency lost to the packet splitting  
  
Ahh, I have figured it out! The problem is `boost::asio::ssl::stream`.  If you use a normal socket, it all goes out in one I/O. I will contact the author of Asio. In the meanwhile, try using the asynchronous interfaces. You can use a coroutine to make it easier. Perhaps the asynchronous SSL implementation will buffer the outgoing write.

---

## Comment 30

> Username: vinniefalco  
> Created at: 2017-06-28 01:11:45 UTC  
> Updated at: 2017-06-28 01:13:18 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311528818  

To be clear, there are two problems here.  
  
1. An inefficiency with `ssl::stream` which doesn't perform the entire I/O in one step, and  
2. The sgcWebSockets server does not correctly handle fragmented HTTP Upgrade requests.  
  
There's a workaround and its pretty straightforward, just implement your own `buffered_write_stream` which passes through reads unchanged, passes through writes with buffer sequences of length 1 unchanged, and for buffer sequences of length 2 or more allocate one buffer and memcpy everything into it then pass it on to the next layer. Once you have this class implemented just declare the beast websocket stream thusly:  
```  
using stream_type =  
    beast::websocket::stream<boost::asio::ssl::stream<  
        buffered_write_stream<boost::asio::ip::tcp::socket>>>;  
```  
  
Here's an example of a stream wrapper which you can copy into a new file and use as a starting point:  
https://github.com/vinniefalco/Beast/blob/3e1061b6d49c114a6f05938986b9a79f82275382/include/beast/core/buffered_read_stream.hpp#L88  
https://github.com/vinniefalco/Beast/blob/master/include/beast/core/impl/buffered_read_stream.ipp#L23  
  
This one does buffering on the read side and passes through writes, but you want the opposite. So just edit the read code to resemble the write code (except, read instead of write) and then edit the write code to do the buffering like I described in the first paragraph. If you make a repository and put this in it and try to work on it, I can help you get it finished.  
  
This function gives you the length of a buffer sequence:  
```  
template<class ConstBufferSequence>  
typename std::enable_if<beast::is_const_buffer_sequence<ConstBufferSequence::value,  
    std::size_t>::type  
length(ConstBufferSequence const& buffers)  
{  
    return std::distance(buffers.begin(), buffers.end());  
}  
```  
  
Note that this is NOT the same as `boost::asio::buffer_size(buffers)`.

---

## Comment 31

> Username: vinniefalco  
> Created at: 2017-06-28 11:12:59 UTC  
> Updated at: 2017-06-28 12:29:53 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311629735  

Can you please confirm that the websocket-client-ssl program in this branch can reproduce your defect? I am communicating with sgc directly, thanks: https://github.com/vinniefalco/Beast/commits/sgc

---

## Comment 32

> Username: larsonmpdx  
> Created at: 2017-06-28 14:48:11 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311682943  

thanks for the workaround steps! I'll try that today

---

## Comment 33

> Username: larsonmpdx  
> Created at: 2017-06-28 14:48:54 UTC  
> Updated at: 2017-06-28 14:50:56 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311683156  

ok I just saw your newest comment, yes I will try  
  
I have asked the server operator for some input, I still don't know if there's some proxy or how they've done the websocket upgrade

---

## Comment 34

> Username: vinniefalco  
> Created at: 2017-06-28 14:59:24 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311686609  

Is Beast otherwise living up to your expectations / fulfilling your needs?

---

## Comment 35

> Username: larsonmpdx  
> Created at: 2017-06-28 15:12:38 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311690875  

absolutely, I have implemented embedded http servers across a whole suite of programs (still need to update using the new framework) and I was able to exorcise curl for simple http GET/POST requests with a short extension/asyncification of the http client example.  Had to do async because there's essentially no support for timeouts in asio sync methods (see this awesome wontfix - https://svn.boost.org/trac10/ticket/2832).  so I recommend a note about this somewhere and maybe an async example or a link to some asio async resources.  I can try to sanitize /release my extension if that would help  
  
getting rid of curl on an embedded target takes my runtime link dependencies down from 50-something to ~17, it's crazy.  curl is extremely hard to statically link on debian and very much embraces the "unix way" of having tons of small supporting elements.  so for a couple GET/POST it gets to be a bit much.  most competitive libraries are similarly huge and can be hard to set up  
  
so, anyway, this fills a big void in simple to set up and non-bloated http tools for c++, thank you

---

## Comment 36

> Username: vinniefalco  
> Created at: 2017-06-28 15:20:37 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311693453  

I hope the synchronous APIs are not removed, I find them quite useful even without the timeout! You can always use coroutines with timeouts, your code will look and flow like synchronous code (except of course it is running in a coroutine). Its very easy to do, I would try it.  
  
I appreciate the thoughtful words.  
  
If you could be so kind, would you consider participating in the Boost formal review of Beast? It will really help the library become even better! Its not a lot of work just write an email or two describing your experience with the library and answer a few standard questions. Just sign up to the "Boost developers mailing list" today or tomorrow here:  
http://www.boost.org/community/groups.html#main  
  
And then wait for the announcement. The review starts on July 1st which is in less than 3 days! They will be very interested to hear from people that have used Beast. You can write as little or as much as you want for the review. And if you have something bad to say about Beast, that's okay too - feedback is what helps improve the library! You might enjoy participating - I always learn a ton of stuff when libraries get reviewed.

---

## Comment 37

> Username: larsonmpdx  
> Created at: 2017-06-28 15:39:12 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311699392  

a coroutine sounds cleaner, async with lambdas gets many levels deep ("callback hell").  I do think it's important to provide an example of how to do timeouts, in whatever way your recommend, since they're so fundamental.  naked sync calls use OS timeouts in general which can be many minutes  
  
sure, I'll sign up  
  
the edited websocket_client_ssl reproduces the problem.  still haven't heard about the specifics of the server like any proxies

---

## Comment 38

> Username: vinniefalco  
> Created at: 2017-06-28 16:01:17 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311706285  

> I do think it's important to provide an example of how to do timeouts  
  
I need to be careful here because I don't want to take responsibility for teaching the world how to use Asio. Its out of scope for Beast. Still, the http-server-fast and http-server-small examples implement timeouts. But not with coroutines unfortunately.  
  
> sure, I'll sign up  
  
Great!! I think you will enjoy it.  
  
>the edited websocket_client_ssl reproduces the problem  
  
Excellent. I have submitted the application to the vendor and they are attempting to reproduce it. I think they had some trouble with the required OpenSSL DLL, hopefully that gets sorted. Thanks.

---

## Comment 39

> Username: larsonmpdx  
> Created at: 2017-06-28 16:57:29 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311722215  

the easiest way to make the client is probably to use ubuntu 17.04 and its default cmake, compiler, boost and openssl-dev packages installed with apt-get.  ubuntu 16.04 is slightly harder because it comes with boost 1.58 and that doesn't have boost::string_view.  and I think its included cmake is a little old.  manually getting the compiler, cmake, boost, and openssl can be a lot for someone not used to c++ dev which is why apt-get can be a good idea

---

## Comment 40

> Username: vinniefalco  
> Created at: 2017-06-28 17:00:03 UTC  
> Updated at: 2017-06-28 17:00:25 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311723008  

They asked for a Windows executable. I fixed Beast to use `string_ref` on older Boosts.

---

## Comment 41

> Username: vinniefalco  
> Created at: 2017-06-28 17:34:19 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311732316  

They got back to me:  
  
>Problem has been fixed, it will be available on next version. Thanks for feedback.

---

## Comment 42

> Username: larsonmpdx  
> Created at: 2017-06-28 19:36:18 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311764653  

using string_ref is a good change, thanks.  only barrier to ubuntu 16.04 is the cmake version which is just a new binary to download  
  
that's great news  
  
here's the start of buffered_write_stream, I have only copied the buffered_read_stream files and done simple renaming so far.  is this the right idea in the websocket example for how it's used?  trying to compile as-is gives me  
  
https://github.com/vinniefalco/Beast/compare/sgc...larsonmpdx:sgc?expand=1  
  
```  
error: use of deleted function ‘boost::asio::basic_stream_socket<boost::asio::ip::tcp>::basic_stream_socket(const boost::asio::basic_stream_socket<boost::asio::ip::tcp>&)’  
     : next_layer_(std::forward<Args>(args)...)  
```

---

## Comment 43

> Username: vinniefalco  
> Created at: 2017-06-28 19:52:58 UTC  
> Updated at: 2017-06-28 19:53:35 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311768732  

You're on the right track. You don't need the multi-buffer. It could probably use an allocator at some point but we can hold off on that.   
  
First you need to edit the `read` and `async_read` functions to just pass through the calls to the next layer.

---

## Comment 44

> Username: vinniefalco  
> Created at: 2017-06-28 19:54:30 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311769127  

Please rename to `flat_write_stream` and put it in `example/common`. You can just append the implementation functions to the end of `flat_write_stream.hpp`, no need to have it in a separate .ipp file.

---

## Comment 45

> Username: vinniefalco  
> Created at: 2017-06-28 19:59:52 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311770728  

Take the `multi_buffer` away and make reads and writes just pass-through, without buffering, get it to compile, and verify that it behaves transparently (i.e. fails the same way it did before without the wrapper).

---

## Comment 46

> Username: vinniefalco  
> Created at: 2017-06-28 20:02:32 UTC  
> Updated at: 2017-06-28 20:03:36 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311771641  

You will need to write this function  
```  
/** Return an iterator to the single non zero buffer if it exists.  
  
    This function returns an iterator to a buffer with a non-zero size if  
    both of the following conditions are met:  
  
    @li The buffer sequence has a non zero length  
  
    @li Exactly one buffer in the sequence has a non-zero size  
  
    Otherwise the function returns `buffers.end()`  
  
    @param buffers The buffer sequence to search through.  
*/  
template<class ConstBufferSequence>  
typename ConstBufferSequence::const_iterator  
find_single(ConstBufferSequence const& buffers);  
```  
  
This can be a static member of `flat_write_stream`

---

## Comment 47

> Username: vinniefalco  
> Created at: 2017-06-28 20:04:57 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311772466  

Your `stream_type` is missing the necessary reference on the socket, it should read:  
```  
using stream_type =  
    boost::asio::ssl::stream<  
        beast::buffered_write_stream<  
            boost::asio::ip::tcp::socket&,    // note &  
            beast::multi_buffer>>;  
```

---

## Comment 48

> Username: larsonmpdx  
> Created at: 2017-06-28 20:05:58 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311772812  

ha yes I just found the reference.  thanks, I will make those changes

---

## Comment 49

> Username: vinniefalco  
> Created at: 2017-06-28 20:07:46 UTC  
> Updated at: 2017-06-28 20:08:10 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311773402  

It doesn't *have* to be a reference, as long as you're willing to write   
```  
ws.next_layer().next_layer().next_layer().connect(tcp::endpoint{address, port}, ec);  
```  
  
You get how these wrappers work right?  
  
:) :) :)

---

## Comment 50

> Username: larsonmpdx  
> Created at: 2017-06-28 20:53:54 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311787095  

when I take away multi_buffer here:  
`using stream_type = boost::asio::ssl::stream<beast::flat_write_stream<boost::asio::ip::tcp::socket&, beast::multi_buffer>>;`  
the template complains:  
```  
error: wrong number of template arguments (1, should be 2)  
```  
  
I moved things around and made the read() stuff pass-through and the client fails the same way  
https://github.com/vinniefalco/Beast/compare/sgc...larsonmpdx:sgc?expand=1  
  
do I need to change the read_some_op() stuff or delete it?

---

## Comment 51

> Username: vinniefalco  
> Created at: 2017-06-28 21:15:30 UTC  
> Updated at: 2017-06-28 21:18:45 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311793924  

>https://github.com/vinniefalco/Beast/compare/sgc...larsonmpdx:sgc?expand=1  
  
This link doesn't work for me (its local to your GitHub login session), you need to use something like this  
https://github.com/larsonmpdx/Beast/commits/sgc  
  
>the template complains:  
  
When I say 'remove the multi buffer' I mean remove it from the call site but also remove the `DynamicBuffer` template parameter from the class template and everywhere its used in the implementation!  
  
>do I need to change the read_some_op() stuff or delete it?  
  
You will need a `write_some_op` so you can take the `read_some_op` and rename it and empty out the implementation of `operator()`, remove whatever you need to in order to make it compile.  
  
Probably put your name at the top, Copyright 2017 and your email if you want, or your GitHub profile link (or nothing)  
  
The include guard is named `BEAST_EXAMPLE_COMMON/FLAT_WRITE_STREAM_HPP`  
  
Don't include `multi_buffer.hpp`  
  
Most everything in there is indented one unnecessary level to the right  
  
You don't need the `buffer` or `capacity` member functions  
  
The read functions look okay.  
  
The write functions you will need to define outside the class since they will be many lines (and we don't want them to be implicitly marked inline).  
  
Add the signature for the function I gave you `find_single` and implement it. Now we will edit the synchronous write function to have this logic:  
  
1. If `buffer_size(buffers)>0` call `find_single` on the `ConstBufferSequence`  
  
2. If `buffer_size(buffers) == 0` or `find_single` returned an iterator just call `next_layer_.write` with a value of `buffers` or `boost::asio::const_buffers_1{*iter}` (the iterator you found) and return the result. This is the efficient/transparent path.  
  
3. Otherwise, if `buffer_size(buffers) <= max_stack_size` where `max_stack_size` is a private class constant set to something like, idk. 4KB, call a private function which declares a stack variable `char buf[max_stack_size]` and uses `boost::asio::buffer_copy` to flatten the input into the variable. Then call `next_layer_.write()` on that variable and return the result.  
  
4. If none of the above then use `std::unique_ptr<char[]> buf{new char[buffer_size(buffers)]}` to get a temporary piece of memory and `boost::asio::buffer_copy` on the input into this temporary memory. Then call `next_layer_.write()` on that temp memory and return the result. You will want to wrap the call to `new`  in a `try/catch` and convert the exception into an `error_code`.  
  
Once you have this compiling and working, you should be able to connect to the sgc server with no trouble. Publish the work, and I will help you write the asynchronous version which lives in the `write_some_op`. It uses the same logic as the synchronous but we have to allocate the memory using the allocation hook and also manage the final completion handler. And of course there is the "inversion of control" that comes with callback style programming.  
  
We can add a test for the both the synchronous and asynchronous versions which uses the `test::pipe` class which simulates a socket. And then this will be part of Beast! How exciting!

---

## Comment 52

> Username: larsonmpdx  
> Created at: 2017-06-28 21:31:07 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311798037  

ok, makes sense.  I rarely write templates so this is a bit of a, uh, growth opportunity.  
  
yeah, I largely ignore formatting these days so I've gotten bad at it.  I just define a .clang-format file and run clang-format before checkin.  It should be easy enough to copy the existing formatting  
  
the rest of that is detailed enough I think I can paint-by-numbers.  thanks for letting me work through it

---

## Comment 53

> Username: vinniefalco  
> Created at: 2017-06-28 21:34:45 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311798893  

>thanks for letting me work through it  
  
Yep! I thought this would be good. The composed operation is the tricky part, there's a tutorial in the documentation although this composed operation is simple enough that it does not need the `handler_ptr`. I will help on any trouble spots. Good luck!

---

## Comment 54

> Username: larsonmpdx  
> Created at: 2017-06-29 01:23:31 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311836136  

I put together enough to test a sync call below max stack size:  
https://github.com/larsonmpdx/Beast/commit/f71467173a4b840875275067b48320d0242d827f  
  
I left some debugging printlines in there, the output is:  
```  
find_single working on 1 buffers  
found a single nonzero buffer  
find_single working on 1 buffers  
found a single nonzero buffer  
find_single working on 1 buffers  
found a single nonzero buffer  
find_single working on 1 buffers  
found a single nonzero buffer  
```  
so it's being called with the individual pieces and the output is unchanged.  Can you take a look?

---

## Comment 55

> Username: vinniefalco  
> Created at: 2017-06-29 01:43:10 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311838797  

* `length()` and `find_single()` should be private  
  
* `capacity_` and `capacity()` is not needed  
  
* In `find_single` you should `BOOST_ASSERT(buffer_size(buffers)>0)`. The caller has to make sure not to pass buffers of size zero. if you get a buffer size of zero you need to pass that straight through to `write`. Please double check against steps 1 and 2 in the outline I posted. This is necessary for `boost::asio::null_buffers` to work correctly:  
http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/overview/core/reactor.html  
  
* Okay in `write_some` it looks like you are only calling `find_single` when `buffer_size(buffers)>0` so you only need to assert in `find_single` rather than have explicit logic.  
  
* As I said in step 3 you want to call a private function which allocates the stack variable otherwise you are always going to be taking up stack space (compilers usually only adjust the stack once upon entry into the function, not on each scope inside the function)  
  
* Lines 315-316 (https://github.com/larsonmpdx/Beast/blob/f71467173a4b840875275067b48320d0242d827f/example/common/flat_write_stream.hpp) contain a bug. When you call `boost::asio::buffer(buf)` in the `write_some` it will always be of size `max_stack_size`. You need to stash the return value of `buffer_copy` in a local variable and construct a `boost::const_buffers_1` from `buf` and the local variable.  
  
* Line 265 the `write_some` which throws the exception should just call the other one instead of duplicating the logic.  Here's an example of how to convert the error into an exception https://github.com/vinniefalco/Beast/blob/78a065ba39836d91d7e70d93de7f9140f518083b/include/beast/http/impl/write.ipp#L740

---

## Comment 56

> Username: vinniefalco  
> Created at: 2017-06-29 02:03:47 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311841542  

oops... found the problem. I got the order of the wrappers wrong. The `flat_write_stream` goes on the outside and the `ssl::stream` goes on the inside. Here's a branch that works:  
https://github.com/vinniefalco/Beast/commits/sgc2

---

## Comment 57

> Username: larsonmpdx  
> Created at: 2017-06-29 02:09:36 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311842359  

awesome, thanks

---

## Comment 58

> Username: larsonmpdx  
> Created at: 2017-06-29 02:11:20 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-311842594  

hey, it works now!  I'll implement the rest of your suggestions and do some cleanup and try tests/async

---

## Comment 59

> Username: vinniefalco  
> Created at: 2017-07-04 01:54:08 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-312761855  

Sounds like this is resolved, thanks! If the problem resurfaces feel free to open a new issue (or re-open this one)

---

## Comment 60

> Username: larsonmpdx  
> Created at: 2017-07-06 00:12:21 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-313260327  

yes, thanks!  working sync functions are enough for me for now.  I do plan to finish this but my schedule is full till August 23

---

## Comment 61

> Username: vinniefalco  
> Created at: 2017-07-06 00:33:08 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-313263116  

Could I talk you into offering a quick Boost formal review? It would take less than an hour, all you have to do is subscribe to the mailing list and make a post with your experiences with the library. The questions to answer are:  
  - What is your evaluation of the design?  
  - What is your evaluation of the implementation?  
  - What is your evaluation of the documentation?  
  - What is your evaluation of the potential usefulness of the library?  
  - Did you try to use the library? With which compiler(s)? Did you  
    have any problems?  
  - How much effort did you put into your evaluation? A glance? A quick  
    reading? In-depth study?  
  - Are you knowledgeable about the problem domain?  
  
It would mean a lot. Sign up here:  
http://www.boost.org/community/groups.html#main  
  
Use the same email to send the message that you use to sign up

---

## Comment 62

> Username: larsonmpdx  
> Created at: 2017-08-25 20:52:55 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-325031809  

I am back.  sgcwebsockets was updated and 4.1.1 has a fix note -  
 http://www.esegece.com/history/sgcWebSockets.txt  
but when I tested this with a branch with and without flat_write_buffer, it appears to not be fixed  
  
I updated flat_write_stream.hpp with the new APIs and put it here with sync and coro examples:  
  
https://github.com/larsonmpdx/Beast/commit/3efcbbd155b2f7e4dd5f7cf9ed88f102f2ca8a4b  
  
can you help me understand what's needed for the async bits?  here is your old comment:  
  
> It uses the same logic as the synchronous but we have to allocate the memory using the allocation hook and also manage the final completion handler. And of course there is the "inversion of control" that comes with callback style programming.  
  
P.S. I had trouble getting things building using boost 1.62 without adding some things to cmakelists (see that commit).  am I misunderstanding something now that it's slated for boost?

---

## Comment 63

> Username: vinniefalco  
> Created at: 2017-08-25 21:44:20 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-325041171  

You shouldn't need your own class anymore if they fixed the bug.  
  
What version of Beast are you using?

---

## Comment 64

> Username: larsonmpdx  
> Created at: 2017-08-25 21:46:14 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-325041465  

I think they didn't fix the bug, or fixed it incorrectly  
  
I rebased to 108

---

## Comment 65

> Username: vinniefalco  
> Created at: 2017-08-25 21:47:45 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-325041697  

CMakeLists.txt in the Boost version of Beast only works with Visual Studio, and you have to put beast "in-tree". That means, you have to put beast at **$BOOST_ROOT/libs/beast**

---

## Comment 66

> Username: larsonmpdx  
> Created at: 2017-08-25 21:49:05 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-325041891  

ah, ok.  the hacked-in bits are working OK so I might just wait till the next boost comes out

---

## Comment 67

> Username: vinniefalco  
> Created at: 2017-08-25 21:51:39 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-325042301  

You shouldn't need Beast's CMakeLists.txt to use Beast in your own projects. Anyway, Beast should be available in Boost 1.66.0 and later :)

---

## Comment 68

> Username: larsonmpdx  
> Created at: 2017-08-25 22:03:33 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-325044220  

so I edited async_write_some to basically do the same thing as write_some, and it seems to work ok.  what's the deal with the allocator and final completion handler you mentioned?

---

## Comment 69

> Username: vinniefalco  
> Created at: 2017-08-25 22:11:17 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-325045436  

Did you push your code? I don't see your composed operation

---

## Comment 70

> Username: larsonmpdx  
> Created at: 2017-08-25 22:22:58 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-325047186  

I pushed after you looked, see here also with some cleanup.   
 I am not handling the allocation error, just returning  
  
https://github.com/larsonmpdx/Beast/commit/d55fa52ce152ffef6b9fda2c5cc35ff609e7dd53  
  
it does work for me.  I still need to learn more about boost coroutines, I am getting an error with my own code.  thanks for the great async and coroutine examples btw

---

## Comment 71

> Username: vinniefalco  
> Created at: 2017-08-25 22:33:32 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-325048676  

The code that you posted is clearly incorrect and also produces undefined behavior. You have no composed operation! `write_some_op` is never constructed or invoked. The implementation of `flat_write_stream::async_write_some` needs to construct an object of type `write_some_op` and invoke it. The instance of `write_some_op` needs to hold all of the state information (especially the `unique_ptr`) which must persist until the handler is invoked. Follow the example code from the documentation:  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_io/writing_composed_operations.html

---

## Comment 72

> Username: larsonmpdx  
> Created at: 2017-08-25 22:38:18 UTC  
> Updated at: 2017-08-25 22:38:25 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-325049321  

obviously I was not understanding this, haha.  that's good documentation, I'll fix things and report back

---

## Comment 73

> Username: larsonmpdx  
> Created at: 2017-08-28 21:30:34 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-325488176  

I did some reading, here are some more references I found:  
  
asio discussion:  
https://sourceforge.net/p/asio/mailman/asio-users/thread/CADH4Q2gCUqt6fnCV51R97HsSeTSZwKP1ban3YWG2YbPqgn_Ebw%40mail.gmail.com/#msg35996833  
  
you reference this project (homekit layer on beast):  
https://github.com/djarek/gabia/blob/master/include/gabia/secure_socket.hpp  
  
detect_ssl.hpp has a similar structure:  
https://github.com/boostorg/beast/blob/master/example/common/detect_ssl.hpp  
  
I still don't understand the overall flow here.  the actual work being done should be trivial but there's lots of boilerplate and I'm having trouble figuring out the templates, asio, and beast  
  
when I instrument the code (and have it set up to not touch any buffers/ change anything) I see this call sequence:  
  
constructor  
continuation  
allocate  
deallocate  
handler_invoke  
operator() with 3 bytes transferred  
  
in looking at the examples they have a state machine set up in operator().  but at this point I'm already seeing 3 bytes transferred and it's too late to combine the buffer - right?  Where should the buffer combining code be placed?

---

## Comment 74

> Username: vinniefalco  
> Created at: 2017-08-28 22:47:51 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-325504685  

You combine the buffer when you construct the composed operation and then in `operator()` just write it.

---

## Comment 75

> Username: larsonmpdx  
> Created at: 2017-09-01 17:38:54 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-326641233  

asio is kinda hard!  I put this together but there is some cargo cult stuff I don't get, can you take a look?  
  
https://github.com/larsonmpdx/Beast/pull/2/files#diff-847b4ba38b786001f950446e30b1ef79  
  
I'm not using a state/ state pointer helper inside the composed op, just shared_ptr for the buffer and pass-by-value for the rest.  does this make sense?  
  
I can't figure out the syntax for the next_layer async_teardown() in case of allocation error:  
  
https://github.com/larsonmpdx/Beast/pull/2/files#diff-847b4ba38b786001f950446e30b1ef79R425  
  
my compiler tells me "‘class boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>&>’ has no member named ‘async_teardown’" and I don't know what to put for the role_type field

---

## Comment 76

> Username: vinniefalco  
> Created at: 2017-09-01 18:36:32 UTC  
> Url: https://github.com/boostorg/beast/issues/548#issuecomment-326654277  

>asio is kinda hard!  
  
Yes, writing a composed asynchronous operation is not for beginners. I'm glad that you're attempting it though.
