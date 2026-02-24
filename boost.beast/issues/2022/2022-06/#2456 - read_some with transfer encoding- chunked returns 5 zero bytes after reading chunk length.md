# #2456 - read_some with transfer encoding: chunked returns 5 zero bytes after reading chunk length [Closed]

> Username: Ilya122  
> Created at: 2022-06-16 12:49:12 UTC  
> Updated at: 2022-06-29 20:38:03 UTC  
> Closed at: 2022-06-29 20:37:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2456  

Hello  
  
### Version of Beast  
BOOST_BEAST_VERSION 322  
  
### Steps necessary to reproduce the problem  
  
Read SSL response with transfer-encoding: chunked  
  
I'm using this type:  
```using SSLStream = boost::asio::ssl::stream<asio::ip::tcp::socket>;```  
  
After connecting and sending the request I begin reading the response using a parser.  
  
First I read the header:  
http::read_header(mStream, mMultiBuff, mParser);  
  
Then I'm detecting a response with no boundary I read everything else in a loop:  
For multi-part I have another reading code.   
  
data is ``std::vector<char>``  
```  
while (!mParser.is_done())  
{  
	auto& body = mParser.get().body();  
	body.data = data.data() + bytesRead;  
	body.size = data.size();  
	body.more = false;  
  
	auto bytes = http::read_some(mStream, mMultiBuff, mParser, error);  
  
	if (isChunk && bytes < chunkThreshholdBytes)  
	{  
		bytes = 0;  
	}  
  
	bytesRead += bytes;  
}  
```  
  
``read_some`` returns 5 bytes and my data vector (Which is ``std::vector<char>``) isn't populated with the length of the chunk, in the picture below we can see it's ``d8d - 3469 bytes``.   
  
A. Possibly I'd like the chunk size to be able to resize the vector only once.   
B. I want to be able to skip these 5 bytes, what happens is ``bytes`` set to 5- but ``data`` has 5 zero bytes in the beginning.   
  
Inside boost reading code, the buffer looks like this:   
![image](https://user-images.githubusercontent.com/4838211/174068437-25dc2e91-f4f5-4209-bbad-bed884d67801.png)  
Red is the end of the headers.  
Cyan is the size of the chunked body.  
Green is the body.   
  
My vector looks like so after reading twice:   
![image](https://user-images.githubusercontent.com/4838211/174072835-22c2cb6e-dfc0-4fd0-bfad-942c25cf96e3.png)  
  
  
### All relevant compiler information  
Using MSVC - VS 2019, Version: 16.11.7  
OpenSSL 1.1.1  
  
My reading code is a bit messy so I wonder if I did something wrong.   
Is this the expected behavior?   
  
Thanks.

---

## Comment 1

> Username: sehe  
> Created at: 2022-06-16 13:16:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1157650265  

Can you share self-contained code? In particular I can't tell what body type you're actually using (assuming buffer_body). Of course you may simplify by dropping ssl, as it is likely not related.

---

## Comment 2

> Username: Ilya122  
> Created at: 2022-06-16 13:27:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1157661632  

> Can you share self-contained code? In particular I can't tell what body type you're actually using (assuming buffer_body). Of course you may simplify by dropping ssl, as it is likely not related.  
  
Sorry I can't share the full code,   
The body is ``http::response_parser<http::buffer_body>``.   
  
We're using only SSL connections, I can try to reproduce it with a simple mock server,   
The response is a chunked json.  
  
For self contained code, it resembles the code from the examples:   
https://www.boost.org/doc/libs/master/libs/beast/example/http/client/sync-ssl/http_client_sync_ssl.cpp  
  
Thanks for quick response

---

## Comment 3

> Username: Ilya122  
> Created at: 2022-06-19 08:42:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1159656978  

> Can you share self-contained code? In particular I can't tell what body type you're actually using (assuming buffer_body). Of course you may simplify by dropping ssl, as it is likely not related.  
  
Hello,  
  
I recreated this scenario using a GTest:   
https://pastebin.com/TWW6upk3  
  
This is rather simple - just add to a json a chunk.

---

## Comment 4

> Username: sehe  
> Created at: 2022-06-19 15:49:21 UTC  
> Updated at: 2022-06-26 23:01:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1159761590  

Great. I'll have a look as soon as I can. Reproducing the sample inline because paste bins tend to expire:  
  
[snip broken code no longer relevant]

---

## Comment 5

> Username: sehe  
> Created at: 2022-06-23 09:42:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1164188240  

Okay, I've been trying to figure out why the code is like it is for a few days now. Turns out this wasn't the best issue to work on to see whether I was recovering from COVID, since I still don't get what you're trying to do/say.  
  
If the complaint is simply that the response length doesn't add up correctly, surely you're missing some form of `bytesRead +=`  step along the way.  
  
But if the goal is /just/ to get things in a `vector<char>`, why not make that the body type and be done? See also https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/more_examples/change_body_type.html  
  
My take:  
  
```c++  
// #include <stdafx.h>  
#include <cassert>  
#include <iostream>  
  
#include <boost/asio/io_context.hpp>  
#include <boost/beast.hpp>  
#include <boost/beast/_experimental/test/stream.hpp>  
  
namespace net = boost::asio;  
namespace beast = boost::beast;  
namespace http = beast::http;  
  
void test_for_github()  
{  
  net::io_context ioc;  
  
  std::string const payload = "[\r\n{\r\n\t\"key\": \"value\"}\r\n]\r\n";  
  
  std::ostringstream sample;  
  sample << "HTTP/1.1 200 OK\r\n"  
         << "Date: Tue, 05 Apr 2022 13:29:09 GMT\r\n"  
         << "Content-Type: application//json\r\n"  
         << "Transfer-Encoding: chunked\r\n"  
         << "\r\n"  
         << std::hex << payload.length() << "\r\n"  
         << payload << "\r\n"  
         << "0\r\n\r\n";  // Empty chunk  
  
  // Act  
  auto local = beast::test::stream{ioc};  
  auto remote = connect(local);  
  
  auto written = net::write(remote, net::buffer(sample.str()));  
  
  http::response_parser<http::buffer_body> mParser;  
  mParser.eager(false);  
  
  beast::multi_buffer mMultiBuff;  
  read_header(local, mMultiBuff, mParser);  
  
  if (mParser.get().chunked()) // is_header_done true by definition  
  {  
    // change the body type  
    http::response_parser<http::vector_body<char>> p{std::move(mParser)};  
    http::read(local, mMultiBuff, p);  
    // done  
  
    auto const& b = p.get().body();  
    std::cout << "Body: " << std::string(b.data(), b.size()) << std::endl;  
  } else {  
     // TODO not chunked  
  }  
}  
  
int main()  
{  
  test_for_github();  
}  
```  
  
Which you can see live on MSVC 19 compiler explorer:  https://godbolt.org/z/3anevEjcj  
  
And the output is  
  
```  
Body: [  
{  
	"key": "value"}  
]  
```

---

## Comment 6

> Username: Ilya122  
> Created at: 2022-06-23 11:11:27 UTC  
> Updated at: 2022-06-23 11:57:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1164278944  

> ```c++  
>     // change the body type  
>     http::response_parser<http::vector_body<char>> p{std::move(mParser)};  
>     http::read(local, mMultiBuff, p);  
>     // done  
>   
>     auto const& b = p.get().body();  
>     std::cout << "Body: " << std::string(b.data(), b.size()) << std::endl;  
> ```  
  
Haha, The workaround also works so don't strain yourself too much on this  
  
---  
  
This works nicely :)   
It will come handy when we deal with post requests as well!  
However this doesn't fix the initial probelm that ``http::read_some`` "populates" zero bytes inside the body.  (As the first message shows)   
  
My initial concern was to adapt to the multipart mimetype , which as I understand Boost doesn' support and I need to parse it by myself.   
Secondly our concern is that we won't be able to output asynchronously (I have a call to ``read_some_async`` as well) every part but will have to wait for the whole message.   
  
So ``http::read`` works for us for a non bounded mime types, but not for the latter case.   
  
Thank you for the help :)

---

## Comment 7

> Username: sehe  
> Created at: 2022-06-23 17:00:57 UTC  
> Updated at: 2022-06-23 17:12:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1164656217  

> However this doesn't fix the initial probelm that http::read_some "populates" zero bytes inside the body. (As the first message shows)  
  
I was unable to reproduce that. Your test case doesn't assert anything relevant to the thing you observe, either.  
  
To me, it looks like the code does probably exactly what you're asking it to do, but I have no idea what you're trying to ask it to do.  
  
If you just remove the stuff you don't need (`mOutBuf`, the loop around `read_header`, magically resizing the `data` vector, and then passing incorrect `body.data.size`) fix the buffer offsets (why do we have `+bytesRead` in some places? That's what DynamicBuffer is supposed to do internally anyways) I strongly suspect the problem will go away too.  
  
I already posted one such version, here's one without changing the body type:  
  
```c++  
  auto& msg = mParser.get();  
  auto& body = msg.body();  
  
  std::vector<char> data(1024);  
  if (msg.chunked())  // is_header_done true by definition  
  {  
    // okay, then, DON'T change the body type  
    body.data = data.data();  
    body.size = data.size();  
  
    auto n = http::read(local, mMultiBuff, mParser);  
    assert(mParser.is_done());  
    data.resize(n);  
  
    std::cout << "Body: " << std::string(data.data(), data.size()) << std::endl;  
  } else  
  {  
    // TODO not chunked  
  }  
```

---

## Comment 8

> Username: sehe  
> Created at: 2022-06-23 17:10:01 UTC  
> Updated at: 2022-06-26 23:07:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1164664851  

If you are concerned with predicting the size, use that dynamic buffer you had, but use it to do the work for you:  
  
```c++  
// #include <stdafx.h>  
#include <cassert>  
#include <iostream>  
  
#include <boost/asio/io_context.hpp>  
#include <boost/beast.hpp>  
#include <boost/beast/_experimental/test/stream.hpp>  
  
namespace net = boost::asio;  
namespace beast = boost::beast;  
namespace http = beast::http;  
  
void test_for_github()  
{  
  net::io_context ioc;  
  
  std::string const payload = "[\r\n{\r\n\t\"key\": \"value\"}\r\n]\r\n";  
  
  std::ostringstream sample;  
  sample << "HTTP/1.1 200 OK\r\n"  
         << "Date: Tue, 05 Apr 2022 13:29:09 GMT\r\n"  
         << "Content-Type: application//json\r\n"  
         << "Transfer-Encoding: chunked\r\n"  
         << "\r\n"  
         << std::hex << payload.length() << "\r\n"  
         << payload << "\r\n"  
         << "0\r\n\r\n";  // Empty chunk  
  
  // Act  
  auto local = beast::test::stream{ioc};  
  auto remote = connect(local);  
  
  auto written = net::write(remote, net::buffer(sample.str()));  
  
  http::response_parser<http::buffer_body> mParser;  
  mParser.eager(false);  
  
  beast::multi_buffer mMultiBuff;  
  read_header(local, mMultiBuff, mParser);  
  
  auto& msg = mParser.get();  
  auto& body = msg.body();  
  
  std::vector<char> data;  
  if (msg.chunked())  // is_header_done true by definition  
  {  
    // okay, then, DON'T change the body type  
    auto dyn = net::dynamic_buffer(data);  
  
    while (!mParser.is_done())  
    {  
      auto buf = dyn.prepare(32);  // use something like 1024 or 4096 of course  
      auto buf_origin = static_cast<char*>(buf.data());  
      body.data = buf.data();  
      body.size = buf.size();  
  
      http::read(local, mMultiBuff, mParser);  
      dyn.commit(static_cast<char*>(body.data) - buf_origin);  
    }  
  
    std::cout << "Body: " << std::string(data.data(), data.size()) << std::endl;  
  } else  
  {  
    // TODO not chunked  
  }  
}  
  
int main()  
{  
  test_for_github();  
}  
```

---

## Comment 9

> Username: Ilya122  
> Created at: 2022-06-26 20:12:49 UTC  
> Updated at: 2022-06-26 23:08:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1166633443  

Sorry I mis clicked.   
  
  
> If you are concerned with predicting the size, use that dynamic buffer you had, but use it to do the work for you:  
>   
>  [snip code example]  
  
  
Do you get into this ``if`` statement?   
```cpp  
	if (isChunk && bytes < chunkThreshholdBytes)  
		{  
			// Chunk body begins with a hexadecimal number which determines the length of chunk and \r\n.  
			// Example:  
			// HEADER\r\n  
			// \r\n  
			// 7\r\n                         <-  This is what we want to skip, the array in the data vector isn't changed so it doesn't matter  
			// ABCDEFG\r\n                       If we could read it properly we'd resize the data array  
			bytes = 0;  
		}  
```  
  
  
As for your solution - for sync code I already replaced it with a read you suggested, of course it works well :)  
My only concern is memory usage, because if I could read it in parts I could reuse the same memory segment.   
We might run into small chunks like 8MB or big files like 500MB or even 2-10GB of data.   
  
Do you think I can reuse it with an ``http::read_async`` as well?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2022-06-26 20:13:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1166633670  

You could use `read_some` and the dynamic buffer body, and just `consume` each part when you are done with it, this will use less memory.

---

## Comment 11

> Username: sehe  
> Created at: 2022-06-26 20:18:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1166634867  

> Do you get into this if statement?  
>   
> 	if (isChunk && bytes < chunkThreshholdBytes)  
  
I did not since I still have no clue what that tries to achieve. Are you somehow manually trying to interpret the chunked encoding? That's what Beast is for.  
  
@vinniefalco   
> You could use read_some and the dynamic buffer body, and just consume each part when you are done with it, this will use less memory.  
  
That's exactly what my [third version](https://github.com/boostorg/beast/issues/2456#issuecomment-1164664851) demonstrates (well, except consuming the dynamic buffer, since we don't know the consumer)

---

## Comment 12

> Username: Ilya122  
> Created at: 2022-06-26 21:08:46 UTC  
> Updated at: 2022-06-26 21:12:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1166645983  

> > Do you get into this if statement?  
> > if (isChunk && bytes < chunkThreshholdBytes)  
>   
> I did not since I still have no clue what that tries to achieve. Are you somehow manually trying to interpret the chunked encoding? That's what Beast is for.  
>   
> @vinniefalco  
>   
> > You could use read_some and the dynamic buffer body, and just consume each part when you are done with it, this will use less memory.  
>   
> That's exactly what my [third version](https://github.com/boostorg/beast/issues/2456#issuecomment-1164664851) demonstrates (well, except consuming the dynamic buffer, since we don't know the consumer)  
  
I think this demonstrates some of the concept, there are four 0 characters in the beginning.  
This is because read_some returns 4 bytes and I advance my buffer.   
  
https://godbolt.org/z/bfd7T9c3E  
The result:  
![image](https://user-images.githubusercontent.com/4838211/175833554-9e560e90-6602-4e0a-82c3-c7b84a23c6a7.png)  
  
DynamicBuffer & ``http::read`` works as expected:  
https://godbolt.org/z/xYh15eqWq  
  
![image](https://user-images.githubusercontent.com/4838211/175833858-b1f2cafe-09e8-444a-93bc-6764e6833012.png)  
  
but with ``http:read_some`` it doesn't:  
https://godbolt.org/z/1PzYP8f46  
![image](https://user-images.githubusercontent.com/4838211/175833806-c4f40ac7-f2c7-4df7-95af-129c33f56936.png)

---

## Comment 13

> Username: sehe  
> Created at: 2022-06-26 23:09:56 UTC  
> Updated at: 2022-06-26 23:13:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1166673118  

I get it now. `read` and `read_some` return counts that don't reflect the effective body content read.  
  
Note that this is **also** true for `http::read` - you just didn't notice it as hard because it didn't and up inserting unexpected data at the front.  
  
You can show and understand this happening by making things more verbose:  
  
```c++  
auto buf = dyn.prepare(1024);  
auto origin_ptr = static_cast<char*>(buf.data());  
body.data = origin_ptr;  
body.size = buf.size();  
  
std::fill_n(origin_ptr, body.size, '*'); // for display contrast  
  
auto n = http::read_some(local, mMultiBuff, mParser);  
auto actual_n = static_cast<char*>(body.data) - origin_ptr;  
std::cout << "Read: " << n << ", actual_n: " << actual_n << "\n";  
dyn.commit(n);  
```  
  
Does print: https://godbolt.org/z/94n1h7538  
  
```  
Read: 3, actual_n: 0  
Read: 3, actual_n: 3  
Read: 7, actual_n: 0  
Payload: length 3 "abc"  
Data: length 13 "***abc*******"  
Valid: 0 "***abc*******"  
```  
  
This means `dyn.commit(n)` and my earlier dynamic-buffer example was wrong. It needs to be the `actual_n` which is kinda hard to get, but it is [documented in `buffer_body`](https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/ref/boost__beast__http__buffer_body__value_type/data.html#beast.ref.boost__beast__http__buffer_body__value_type.data.when_parsing):   
  
> Otherwise, the parser will store body octets into the memory pointed to by data having size octets of valid storage. After octets are stored, the data and size members are adjusted: **`data` is incremented to point to the next octet after the data written**, while `size` is decremented to reflect the remaining space at the memory location pointed to by `data`.  
  
Here's fixed, also fixing above so there is no incorrect sample code anymore in this issue: https://godbolt.org/z/WsfYbejjs  
  
```c++  
// #include <stdafx.h>  
#include <cassert>  
#include <iomanip>  
#include <iostream>  
  
#include <boost/asio/io_context.hpp>  
#include <boost/beast.hpp>  
#include <boost/beast/_experimental/test/stream.hpp>  
  
namespace net = boost::asio;  
namespace beast = boost::beast;  
namespace http = beast::http;  
  
void test_for_github()  
{  
  net::io_context ioc;  
  
  std::ostringstream sample;  
  
  std::string const payload{"[\r\n"  
                            "{\r\n"  
                            "	\"key\": \"value\""  
                            "}\r\n"  
                            "]\r\n"};  
  
  sample << "HTTP/1.1 200 OK\r\n"  
         << "Transfer-Encoding: chunked\r\n"  
         << "\r\n"  
         << std::hex << payload.length() << "\r\n"  
         << payload << "\r\n"  
         << "0\r\n\r\n"; // Empty chunk  
  
  // Act  
  auto local = beast::test::stream{ioc};  
  auto remote = connect(local);  
  
  //std::cout << "SENDING: " << std::quoted(sample.str()) << "\n";  
  /*auto written =*/ net::write(remote, net::buffer(sample.str()));  
  
  http::response_parser<http::buffer_body> mParser;  
  mParser.eager(false);  
  
  beast::multi_buffer mMultiBuff;  
  read_header(local, mMultiBuff, mParser);  
  
  auto& msg = mParser.get();  
  auto& body = msg.body();  
  
  std::string data;  
  
  if(msg.chunked()) // is_header_done true by definition  
  {  
      auto dyn = net::dynamic_buffer(data);  
      while(! mParser.is_done())  
      {  
          auto buf = dyn.prepare(1024);  
          auto buf_origin = static_cast<char*>(buf.data());  
          body.data = buf_origin;  
          body.size = buf.size();  
  
          http::read_some(local, mMultiBuff, mParser);  
          dyn.commit(static_cast<char*>(body.data) - buf_origin);  
      }  
  
      std::cout << "Valid: " << std::boolalpha << (payload == data) << " "  
                << std::quoted(data) << "\n";  
  } else  
  {  
      // TODO not chunked  
  }  
}  
  
int  
main()  
{  
    test_for_github();  
}  
```

---

## Comment 14

> Username: sehe  
> Created at: 2022-06-26 23:24:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1166676691  

Late addition: it might be cleaner to use the documented modification of `body.size` instead of `body.data`:  
  
```c++  
while(! mParser.is_done())  
{  
    auto buf = dyn.prepare(1024);  
    auto n = buf.size();  
    body.data = buf.data();  
    body.size = n;  
  
    http::read_some(local, mMultiBuff, mParser);  
    dyn.commit(n - body.size);  
}  
```  
  
The effect is documented to be the same: https://godbolt.org/z/x4E4Tc5xG

---

## Comment 15

> Username: Ilya122  
> Created at: 2022-06-27 15:31:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1167502113  

> Late addition: it might be cleaner to use the documented modification of `body.size` instead of `body.data`:  
>   
> ```c++  
> while(! mParser.is_done())  
> {  
>     auto buf = dyn.prepare(1024);  
>     auto n = buf.size();  
>     body.data = buf.data();  
>     body.size = n;  
>   
>     http::read_some(local, mMultiBuff, mParser);  
>     dyn.commit(n - body.size);  
> }  
> ```  
>   
> The effect is documented to be the same: https://godbolt.org/z/x4E4Tc5xG  
  
I replaced my code with this and it works better now :)  
  
Still my only concern is how to manage the memory so I can actually just resize ( With dynamic_buffer it's ``prepare``) only once per chunk size?  
Or it shouldn't bother me?    
  
I'll try to play around more with the buffers.

---

## Comment 16

> Username: sehe  
> Created at: 2022-06-27 20:46:53 UTC  
> Updated at: 2022-06-27 20:47:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1167873517  

> Or it shouldn't bother me?  
  
It shouldn't bother you. The chunk size has no application relevance. Neither does it have transport relevance (meaning: chunksize == N doesn't say that the network packets will arrive in any particular size. They may be much smaller, or indeed send multiple chunks in one packet). Its only relevance is the encoding, which is 100% hidden from you using `http::parser`.  
  
Just do as you would do any read: *you* decide on a suitable buffer size given what you know about your application and that will do.

---

## Comment 17

> Username: Ilya122  
> Created at: 2022-06-28 12:25:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1168655202  

> > Or it shouldn't bother me?  
>   
> It shouldn't bother you. The chunk size has no application relevance. Neither does it have transport relevance (meaning: chunksize == N doesn't say that the network packets will arrive in any particular size. They may be much smaller, or indeed send multiple chunks in one packet). Its only relevance is the encoding, which is 100% hidden from you using `http::parser`.  
>   
> Just do as you would do any read: _you_ decide on a suitable buffer size given what you know about your application and that will do.  
  
Thanks!  
  
Regarding the solution purposed here:   
https://godbolt.org/z/x4E4Tc5xG  
  
Now it's overriding the data because when size > 1024 it resizes it and returns the beginning of the buffer which is not good.  
So I still need to calculate the exact location of the buffer for another write.  
  
How can this be avoided?

---

## Comment 18

> Username: sehe  
> Created at: 2022-06-28 13:20:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1168716917  

> Overriding  
  
I don't know what the means  
  
>  it resizes it  
  
What resizes what?  
  
> returns the beginning of the buffer   
  
What does?  
  
> So I still need to calculate the exact location of the buffer for another write.  
  
`dyn.prepare` already does exactly those things and correctly

---

## Comment 19

> Username: Ilya122  
> Created at: 2022-06-28 13:27:10 UTC  
> Updated at: 2022-06-28 13:35:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1168724441  

> So I still need to calculate the exact location of the buffer for another write.  
  
This is part of my reading code:  
I need to prepare the dynamic buffer only after some actions occured so I used a ``unique_ptr``.  
```cpp  
using DynamicBufferString = boost::asio::dynamic_string_buffer<std::string::value_type, std::string::traits_type, std::string::allocator_type>;  
  
mDynamicBuffer = std::make_unique<DynamicBufferString>(mStringBuffer);  
```  
  
```cpp  
// this is what happens "after read"  
auto size = mParser.get().body().size;  
auto realTotalRead = mLastBufferSizeRead == 0 ? bytes_transferred : mLastBufferSizeRead - size;  
mTotalBytesRead += (realTotalRead);  
mDynamicBuffer->commit(mTotalBytesRead);  
  
// This is what happenes before next read   
// Check for first read?   
auto toPrepare = realTotalRead != 0 ? realTotalRead : 1024;  
auto buf = mDynamicBuffer->prepare(toPrepare);  
  
auto n = buf.size();  
auto& msg = mParser.get();  
auto& body = msg.body();  
body.data = buf.data();  
body.size = n;  
mLastBufferSizeRead = n;  
  
beast::get_lowest_layer(mStream).expires_after(std::chrono::minutes(5));  
  
http::async_read_some(mStream, mMultiBuff, mParser, beast::bind_front_handler(&MY_CLASS::OnRead, this));  
```				  
  
I also tried ``async_read_some``.  
  
But my underlying buffer ``mStringBuffer`` is overriden in each read from the beginning of the buffer.  
I expect the value of the boundary since it's multipart (``--my_boundary_example``) and after the second/third read the string is overriden.  
  
Other caveat I have is when I resize it by 1024 it creates "holes" of 0's inside ``mStringBuffer``.

---

## Comment 20

> Username: sehe  
> Created at: 2022-06-28 13:30:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1168728938  

> dyn.prepare already does exactly those things and correctly  
  
Because code talks: https://godbolt.org/z/8b8dMvbGf  
  
```c++  
// #include <stdafx.h>  
#include <cassert>  
#include <iomanip>  
#include <iostream>  
  
#include <boost/asio/io_context.hpp>  
#include <boost/beast.hpp>  
#include <boost/json/src.hpp> // for header-only  
#include <boost/beast/_experimental/test/stream.hpp>  
  
namespace net = boost::asio;  
namespace beast = boost::beast;  
namespace http = beast::http;  
  
void test_for_github()  
{  
  net::io_context ioc;  
  
  std::ostringstream sample;  
  
  boost::json::object obj;  
  for (auto i = 0; i<1000; ++i)  
  {  
      auto s = std::to_string(i);  
      obj.emplace("key" + s, "value" + s);  
  }  
  
  std::string const payload = serialize(obj);  
  
  sample << "HTTP/1.1 200 OK\r\n"  
         << "Transfer-Encoding: chunked\r\n"  
         << "\r\n"  
         << std::hex << payload.length() << "\r\n"  
         << payload << "\r\n"  
         << "0\r\n\r\n"; // Empty chunk  
  
  // Act  
  auto local = beast::test::stream{ioc};  
  auto remote = connect(local);  
  
  //std::cout << "SENDING: " << std::quoted(sample.str()) << "\n";  
  /*auto written =*/ net::write(remote, net::buffer(sample.str()));  
  
  http::response_parser<http::buffer_body> mParser;  
  mParser.eager(false);  
  
  beast::multi_buffer mMultiBuff;  
  read_header(local, mMultiBuff, mParser);  
  
  auto& msg = mParser.get();  
  auto& body = msg.body();  
  
  std::string data;  
  if(msg.chunked()) // is_header_done true by definition  
  {  
      auto dyn = net::dynamic_buffer(data);  
      while(! mParser.is_done())  
      {  
          auto buf = dyn.prepare(1024);  
          std::cout << "offset: "  
                    << static_cast<char const*>(buf.data()) - data.data()  
                    << " ";  
  
          auto n = buf.size();  
          body.data = buf.data();  
          body.size = n;  
  
          auto wire_transferred = http::read_some(local, mMultiBuff, mParser);  
          dyn.commit(n - body.size);  
  
          std::cout << "wire_transferred: " << wire_transferred << " cumulative data: " << data.size() << "\n";  
      }  
  
      std::cout << "Valid: " << std::boolalpha << (payload == data) << "\n";  
  } else  
  {  
      // TODO not chunked  
  }  
}  
  
int  
main()  
{  
    test_for_github();  
}  
```  
  
Output  
  
```  
offset: 0 wire_transferred: 6 cumulative data: 0  
offset: 0 wire_transferred: 459 cumulative data: 459  
offset: 459 wire_transferred: 512 cumulative data: 971  
offset: 971 wire_transferred: 512 cumulative data: 1483  
offset: 1483 wire_transferred: 512 cumulative data: 1995  
offset: 1995 wire_transferred: 512 cumulative data: 2507  
offset: 2507 wire_transferred: 512 cumulative data: 3019  
offset: 3019 wire_transferred: 512 cumulative data: 3531  
offset: 3531 wire_transferred: 512 cumulative data: 4043  
offset: 4043 wire_transferred: 512 cumulative data: 4555  
offset: 4555 wire_transferred: 512 cumulative data: 5067  
offset: 5067 wire_transferred: 512 cumulative data: 5579  
offset: 5579 wire_transferred: 512 cumulative data: 6091  
offset: 6091 wire_transferred: 512 cumulative data: 6603  
offset: 6603 wire_transferred: 512 cumulative data: 7115  
offset: 7115 wire_transferred: 512 cumulative data: 7627  
offset: 7627 wire_transferred: 512 cumulative data: 8139  
offset: 8139 wire_transferred: 512 cumulative data: 8651  
offset: 8651 wire_transferred: 512 cumulative data: 9163  
offset: 9163 wire_transferred: 512 cumulative data: 9675  
offset: 9675 wire_transferred: 512 cumulative data: 10187  
offset: 10187 wire_transferred: 512 cumulative data: 10699  
offset: 10699 wire_transferred: 512 cumulative data: 11211  
offset: 11211 wire_transferred: 512 cumulative data: 11723  
offset: 11723 wire_transferred: 512 cumulative data: 12235  
offset: 12235 wire_transferred: 512 cumulative data: 12747  
offset: 12747 wire_transferred: 512 cumulative data: 13259  
offset: 13259 wire_transferred: 512 cumulative data: 13771  
offset: 13771 wire_transferred: 512 cumulative data: 14283  
offset: 14283 wire_transferred: 512 cumulative data: 14795  
offset: 14795 wire_transferred: 512 cumulative data: 15307  
offset: 15307 wire_transferred: 512 cumulative data: 15819  
offset: 15819 wire_transferred: 512 cumulative data: 16331  
offset: 16331 wire_transferred: 512 cumulative data: 16843  
offset: 16843 wire_transferred: 512 cumulative data: 17355  
offset: 17355 wire_transferred: 512 cumulative data: 17867  
offset: 17867 wire_transferred: 512 cumulative data: 18379  
offset: 18379 wire_transferred: 512 cumulative data: 18891  
offset: 18891 wire_transferred: 512 cumulative data: 19403  
offset: 19403 wire_transferred: 378 cumulative data: 19781  
offset: 19781 wire_transferred: 7 cumulative data: 19781  
Valid: true  
```

---

## Comment 21

> Username: Ilya122  
> Created at: 2022-06-28 18:56:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1169106258  

>   
  
That was one confusing code.   
I got it working after I removed my previous ``GrowBuffer`` method which tried to replicate.   
I've put a more simple version - to be precise like your code - and it works :)   
  
Still the only thing that bothers me is when I read Content-Length I don't want to ``resize`` my string every reading.  
This slows down the process, so I optimize it by the ``prepare`` method.  
Do I still need to call ``commit``?   
  
Thanks for all your help!

---

## Comment 22

> Username: sehe  
> Created at: 2022-06-28 20:19:05 UTC  
> Updated at: 2022-06-28 20:19:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1169191704  

If you have chunked encoding you usually don't know the content-length ahead of time. Also there is a potential for conflicting information where you have a content-length that doesn't match the effective chunked transfer size. You can always just reserve the `vector` or whatever eventually contains your end result.  
  
Yes  you obviously need prepare & commit because like you noticed they do the offset calculations. If the underlying container was suitably `reserved()` there will be no slow-down.  
  
But again, if at the end of the road you still only want to receive into a `vector<char>`, by all means [go back to my very first example](https://github.com/boostorg/beast/issues/2456#issuecomment-1164188240) and use `vector_body<char>`

---

## Comment 23

> Username: vinniefalco  
> Created at: 2022-06-28 21:13:59 UTC  
> Updated at: 2022-06-28 21:14:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1169249590  

> If you have chunked encoding...  
  
You NEVER know the Content-Length ahead of time, because HTTP messages containing both chunked Transfer-Encoding AND a Content-Length field are _invalid_. Beast's parser produces an error on such messages.

---

## Comment 24

> Username: Ilya122  
> Created at: 2022-06-29 20:37:10 UTC  
> Updated at: 2022-06-29 20:38:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2456#issuecomment-1170470073  

> > If you have chunked encoding...  
>   
> You NEVER know the Content-Length ahead of time, because HTTP messages containing both chunked Transfer-Encoding AND a Content-Length field are _invalid_. Beast's parser produces an error on such messages.  
  
You're probably right, the http server I work against do all kind of magic so who knows.   
  
  
@sehe  @vinniefalco   
Thanks for all the help :)
