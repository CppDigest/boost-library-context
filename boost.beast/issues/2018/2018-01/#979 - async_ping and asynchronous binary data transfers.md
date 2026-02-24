# #979 - async_ping and asynchronous binary data transfers [Closed]

> Username: rberlich  
> Created at: 2018-01-13 19:17:56 UTC  
> Updated at: 2018-01-15 13:10:52 UTC  
> Closed at: 2018-01-15 13:10:52 UTC  
> Url: https://github.com/boostorg/beast/issues/979  

As I am having some problems getting binary data transfers (with data coming from Boost.Serialization with binary archives) to work with Beast in Websocket mode: I am trying to understand whether setting ws.binary(true) affects the handshake and pings. From what  I can see, pings just send static_string objects, which I suspect know nothing about binary transfers. So setting ws.binary(true) globally in the client- and server-session classes is not an option. However, with async_read/write, setting them per message is also not possible, at least when fragmentation is allowed. Or am I overthinking this again ?  :-)  
  
This is with Boost 1.66 on Linux (Ubuntu 17.10)  
  
Sorry and Best Regards,  
Beet

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-14 00:08:39 UTC  
> Updated at: 2018-01-14 00:13:24 UTC  
> Url: https://github.com/boostorg/beast/issues/979#issuecomment-357477789  

The WebSocket protocol defines two categories of message frames: binary, and text. The only difference is that text message payloads must be valid utf-8, while binary message payloads may have arbitrary octets. The message payload is defined as the concatenation of the first frame and any subsequent continuation frames. So if a utf-8 code point is split between two frames, that's okay.  
  
The handshake, pings, pongs, and close frames are not affected by the binary setting on the `beast::websocket::stream`. Note that as per the WebSocket RFC, pings and pongs have binary payloads while the text in the close frame payload must be valid utf8 text.  
  
That said, you can call `ws.binary(true)` or `ws.binary(false)` as often as you'd like, Beast saves the setting when it begins sending a message so even if you change it while you are in the middle of a fragmented message, it will only take effect after that message is finished.

---

## Comment 2

> Username: rberlich  
> Created at: 2018-01-15 13:10:52 UTC  
> Url: https://github.com/boostorg/beast/issues/979#issuecomment-357678629  

Hi there,   
  
I have finally found the problem with the binary data transfers -- I have at one point converted the correctly received std::string to a C-string using string::c_str(), which has altered the string (by adding a 0-termination, I assume). boost.serialization couldn't cope with this in binary mode (with XML- or text-transfers this wasn't an issue, of course).  
  
Comparing the string before sending with and after reception by Beast didn't show a difference, so I was struggling to understand where the problem came from. Entirely my fault :-)  
  
Thanks for the comprehensive information!  
  
Kind Regards,  
Beet
