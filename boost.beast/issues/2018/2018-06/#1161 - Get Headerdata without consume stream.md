# #1161 - Get Headerdata without consume stream... [Closed]

> Username: ngohr  
> Created at: 2018-06-13 13:31:42 UTC  
> Updated at: 2018-07-25 17:03:45 UTC  
> Closed at: 2018-07-25 17:03:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1161  

I try to extract header data before accepting a ssl websocket connection.  
But no luck since two days...  
  
If i do something like:  
  
		std::cout << "Read out Headers\n";  
		boost::asio::streambuf header;  
		boost::asio::read_until(_stream, header, "\r\n\r\n");  
		std::ostringstream strsresult;  
		strsresult << boost::beast::buffers(header.data());  
		std::cout << strsresult.str() << "\n";  
  
After etablishing the stream, but before the standard read process....  
I cannot read the bytes before and give them to accept, because the full ssl handshake must happen before.  
Couldt i read the headers from stream without consuming the bytes, so the standard functions couldt work anyway?  
I need the cookie data, before a wss connection will etablished.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-13 13:53:21 UTC  
> Updated at: 2018-06-13 13:53:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1161#issuecomment-396945278  

This is all explained in the documentation.  
  
For clients:  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/using_websocket/handshaking_clients.html#beast.using_websocket.handshaking_clients.filtering  
  
For servers:  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/using_websocket/handshaking_servers.html

---

## Comment 2

> Username: ngohr  
> Created at: 2018-06-13 14:09:55 UTC  
> Updated at: 2018-06-13 14:11:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1161#issuecomment-396951082  

Thank you, but   
  
ws.accept(buffer.data());  
  
does not work because the http headers appears in the buffer after  
  
stream.handshake(ssl::stream_base::server, ec);  
  
but before ws_.next_layer().async_handshake  
  
(or something else)  
  
so handshake fails and i never came to ws.accept();  
  
My best tries ends up without handshake functions but with fails(): accept: uninitialized  
This happens if i read out something from stream before, so i think the data needed was consumed by my hackarround.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-06-13 14:25:39 UTC  
> Updated at: 2018-06-13 14:26:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1161#issuecomment-396956719  

>ws.accept(buffer.data());  
> does not work because the http headers appears in the buffer after  
  
Why are you using the overload of accept which takes a buffer sequence? That is not the instruction given in the documentation. You are supposed to read the HTTP request yourself, **using beast**. The example code in the documentation is clear:  
  
From  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/using_websocket/handshaking_servers.html#beast.using_websocket.handshaking_servers.passing_http_requests  
  
```  
// Read the HTTP request ourselves  
http::request<http::string_body> req;  
http::read(sock, buffer, req);  
```  
  
Then  
```  
    // Accept the upgrade request  
    ws.accept(req);  
```  
  
Was the documentation unclear on this point? The advanced server examples also demonstrate this:  
https://github.com/boostorg/beast/blob/93c35524a6125db3e6aeefc8abc826a810dd5d61/example/advanced/server-flex/advanced_server_flex.cpp#L262  
  
And in the reference:  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/accept/overload5.html

---

## Comment 4

> Username: ngohr  
> Created at: 2018-06-14 09:53:39 UTC  
> Updated at: 2018-06-14 11:21:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1161#issuecomment-397239333  

> Why are you using the overload of accept which takes a buffer sequence?  
I thougt that i must pass the abused buffer (see below) to merge it with the next ::read call  
  
I tought that i can pass the buffer of read_until to the overloaded methods to include the buffer that was allready read...  
  
The documentation does not include how http::request< Body, http::basic_fields< Allocator >> const& req shouldt be passed on ssl streams.  
In the advanced_server_flex you initiate _req at line 760 and dont touch it since ssl_httpsession calls doRead()  
But that does not work in my simple code:  
  
`  
boost::beast::error_code ec;  
boost::asio::streambuf header;  
boost::beast::flat_buffer buffer;  
  
// Block until we get a connection  
acceptor.accept(socket);  
  
// I dont know where i have to place that i tried it often...  
http::request<boost::beast::http::string_body> req;  
  
// Construct the stream around the socket  
ssl::stream<tcp::socket&> stream{socket, ctx};  
stream.handshake(ssl::stream_base::server, ec);  
  
boost::asio::read_until(stream, header, "\r\n\r\n", ec);  
std::ostringstream strheader;  
strheader << boost::beast::buffers(header.data());  
std::cout << strheader.str() << "\n";  
  
// req = {};  
  
/*  
 * This Blocks completly and the prog doesnt reach the next lines  
 * std::cout << "Try to read from request\n";  
 * boost::beast::http::read(socket, buffer, req, ec);  
 * std::ostringstream strsresult;  
 * strsresult << boost::beast::buffers(buffer.data());  
 * std::cout << strsresult.str() << "\n";  
 *  
 */  
  
// See if its a WebSocket upgrade request  
if(websocket::is_upgrade(req)) {  
	// Returns true with Plain ws and without the handshake above  
	std::cout << "Here in upgrade\n";  
} else {  
	// With the read_until above this returns false, i think because the header line was allready read  
	// Without the ssl handshake above it returns false, because i think the headers are encrypted  
	std::cout << "Handle HTTP\n";  
}  
`  
  
With the read_until after the handshake, i get the expected result, but not a ::read a ::handshake neither ::is_upgrade works.  
So i think i abuse the buffer behind the stream. THE Stream that imho does not have any relation to the request element.  
I am so sorry but i dont understand in which case i can use the request element in an ssl stream, neither where i a have to instance it.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-06-14 13:24:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1161#issuecomment-397294645  

> I tought that i can pass the buffer of read_until to the overloaded methods to include the buffer that was allready read...  
  
If you pass a buffer, then you can't see the headers. If you want to see the headers, you have to read the HTTP request **using beast** (`beast::http::read` or `beast::http::async_read`).  
  
Again:  
  
```  
websocket::stream<ssl::stream<tcp::socket>> stream(ioc, ctx);  
...  
http::request<http::string_body> req;  
http::read(stream.next_layer(), req);  
stream.accept(req);  
```

---

## Comment 6

> Username: ngohr  
> Created at: 2018-06-18 14:41:35 UTC  
> Updated at: 2018-06-18 14:42:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1161#issuecomment-398077928  

This:  
  
`  
  
  
		boost::beast::flat_buffer header;  
  
		boost::system::error_code ec;  
  
		boost::beast::http::read(ws_.next_layer(), header, req);  
  
		std::ostringstream strsresult;  
  
		strsresult << boost::beast::buffers(header.data());  
  
		std::cout << strsresult.str() << "\n";  
  
		std::cout << "------------------------------------------------\n";`  
  
  
Placed anywhere, after ssl handshake in advanced flex server, blocks the whole application.  
Allready tried with moved sockets, sslstream, websocketstream layers and so on...  
  
My first try was to Place it before   
  
derived().ws().async_accept  
  
with   
  
derived().ws().next_layer()

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-06-18 15:44:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1161#issuecomment-398099866  

Beast requires an understanding of Boost.Asio. You are performing a synchronous operation in an asynchronous context, thus blocking the server. I suggest you take some time to first study and understand Asio's execution models, and then revisit Beast once you are more familiar with it.

---

## Comment 8

> Username: stale[bot]  
> Created at: 2018-07-18 16:30:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1161#issuecomment-405993573  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: stale[bot]  
> Created at: 2018-07-25 17:03:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1161#issuecomment-407825945  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
