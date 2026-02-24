# #2273 - boost::beast::buffers_range_ref [Closed]

> Username: envymenot  
> Created at: 2021-07-01 04:07:21 UTC  
> Updated at: 2021-07-01 04:36:19 UTC  
> Closed at: 2021-07-01 04:36:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2273  

### Version of Beast BOOST_BEAST_VERSION 313 (boost_1_76_0)  
g++ (Ubuntu 9.3.0-17ubuntu1~20.04) 9.3.0  
  
Dears,  
first of all I'd like to thank you, **really thank** all of you for the efforts providing such amazing libraries, they make our life definitely much easier.. :)  
  
I'm using Beast as a websocket client and I'd like to move from "buffers_to_string" to "buffers_range_ref".  
At the moment I'm encountering some differences between debug and release compilations using 'boost::beast::buffers_range_ref'.  
  
I've reproduced the issue with the async-ssl websocket sample:  
websocket_client_async_ssl.cpp  (boost_1_76_0/libs/beast/example/websocket/client/async-ssl)  
  
I've just modified the 'on_read' method adding what I found here: https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__buffers_range_ref.html  
  
		std::cout << "bytes_transferred: " << bytes_transferred << std::endl;  
		std::size_t size = 0;  
		for (auto const buffer : boost::beast::buffers_range_ref(buffer_.data())) {  
			std::cout << "buffer size: " << buffer.size() << std::endl;  
			size += buffer.size();  
		}  
		std::cout << "total size: " << size << std::endl;  
  
*I'm attaching the sample project. (Eclipse CDT)  
[beast_test.zip](https://github.com/boostorg/beast/files/6745269/beast_test.zip)  
  
It seems the 'single' buffer size is correct in the debug build, but get some issue in the release one:  
  
**DEBUG:**  
g++ -I/path_to/boost_1_76_0 -O0 -g3 -Wall -c -fmessage-length=0 -MMD -MP -MF"main.d" -MT"main.o" -o "main.o" "../main.cpp"  
g++ -pthread -o "beast_test"  ./main.o   -lssl -lcrypto  
  
OUTPUT:  
./beast_test echo.websocket.org 443 "Hello, world!"  
bytes_transferred: 13  
buffer size: 13  
total size: 13  
  
  
**RELEASE:**  
g++ -I/path_to/boost_1_76_0 -O3 -Wall -c -fmessage-length=0 -MMD -MP -MF"main.d"   
g++ -pthread -o "beast_test"  ./main.o   -lssl -lcrypto  
  
OUTPUT:  
./beast_test echo.websocket.org 443 "Hello, world!"  
bytes_transferred: 13  
buffer size: 94420242014400  
total size: 0  
  
*Note:  
Using the 'boost::beast::buffers_range' function instead, both versions work as expected.  
  
  
Thank you for any suggestion for solving my problem.  
  
Kind regards  
Emiliano

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-07-01 04:22:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2273#issuecomment-871905802  

You're constructing from a temporary:  
```  
boost::beast::buffers_range_ref(buffer_.data())  
```  
  
As the name implies, `buffers_range_ref` stores a reference to the target buffer, which is destroyed after the expression is evaluated - leading to undefined behavior.

---

## Comment 2

> Username: envymenot  
> Created at: 2021-07-01 04:35:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2273#issuecomment-871910263  

Dear Vinnie Falco,  
thank you! I've got the point:  
"...The application is responsible for ensuring that the lifetime of the referenced buffers extends until the range object is destroyed."  
  
		auto _buffers = buffer_.data();  
		for (auto const buffer : boost::beast::buffers_range_ref(_buffers)) { ..  
  
Works perfectly.
