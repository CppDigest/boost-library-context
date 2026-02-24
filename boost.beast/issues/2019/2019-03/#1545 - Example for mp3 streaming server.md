# #1545 - Example for mp3 streaming server? [Closed]

> Username: JFreyberger  
> Created at: 2019-03-26 17:30:08 UTC  
> Updated at: 2019-03-28 17:21:27 UTC  
> Closed at: 2019-03-28 17:21:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1545  

Dear Sirs,  
  
I'm running the advanced server example and now I'm trying to add the functionality of an audio (opus or mp3) live streaming server. So I'll have to answer this request with an HTTP response 200 header Content-Type: audio-mpeg and some other lines like "icy-br: 56" etc. followed by the raw audio data. Is there also an example available for such a scenario? I tried something like a websocket to send the audio data continuously or to stick with the http_session. But both approaches probably are not correct.   
  
Thanks a lot in advance,  
Johannes

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-26 17:34:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1545#issuecomment-476763813  

There are no audio-specific examples, but you could specify a "chunked" transfer-encoding in your header. Serialize the HTTP header first using `http::write_header`, and then emit the audio content in chunks using the chunking interfaces:  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/using_http/chunked_encoding.html

---

## Comment 2

> Username: JFreyberger  
> Created at: 2019-03-26 20:54:31 UTC  
> Updated at: 2019-03-26 20:57:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1545#issuecomment-476847813  

Thanks for this tip. I already tried this chunked encoding but so far it doesn't work. I'd like to achive a response like I'm seeing in wireshark when I use an element like `<audio src="http://br-br1-obb.cast.addradio.de/br/br1/obb/mp3/56/stream.mp3" controls></audio>` in my html source. There I did not see the chunked encoding, but just a quite normal HTTP response followed by mp3 packets. For sure I have to learn more about the templates and lambdas etc. used in your code but if you're just used to old style C++ - like I am - it's hard to learn and read the compiler and debug output correct when you do some code modifications.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-03-26 21:27:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1545#issuecomment-476859549  

Well if you use `http::response<http::string_body>` you can just put whatever you want into the body, as a string, and it will be delivered.  
  
> if you're just used to old style C++ - like I am  
  
Is C++11 old style?

---

## Comment 4

> Username: JFreyberger  
> Created at: 2019-03-27 08:59:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1545#issuecomment-477046994  

> Is C++11 old style?  
  
No, not at all and also your code seems to make sophisticated use of all the new C++ features. Sorry for this misunderstanding. I wanted to say that my knowledge about C++ is old style and I'm not good in reading templates and lambdas etc. and so I'm fighting the compiler outputs and the callstacks when I'm doing some modifications on your examples.   
  
As far as I understand it I have to keep the mp3 HTTP session open after sending the header and send the raw audio data chunks (which are binary data) in intervals but on the other hand this seems to be no websocket and I'm searching for the correct approach to add this to the advanced server example.

---

## Comment 5

> Username: JFreyberger  
> Created at: 2019-03-28 17:21:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1545#issuecomment-477692407  

OK, seems to work now :-)
