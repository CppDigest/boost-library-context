# #431 - HTTPS bad chunk when reading response [Closed]

> Username: maddinat0r  
> Created at: 2017-06-07 20:11:20 UTC  
> Updated at: 2017-06-08 07:02:48 UTC  
> Closed at: 2017-06-08 03:14:43 UTC  
> Url: https://github.com/boostorg/beast/issues/431  

### Version of Beast  
  
1.0.0-v50 (ff01d04e9823a8c301f044d1a5ac0e5d2c5a1ce5)  
  
### Steps necessary to reproduce the problem  
  
```cpp  
#include <thread>  
  
#include <beast/core.hpp>  
#include <beast/http.hpp>  
#include <beast/websocket.hpp>  
#include <beast/websocket/ssl.hpp>  
#include <boost/asio.hpp>  
#include <boost/asio/ssl.hpp>  
#include <boost/asio/steady_timer.hpp>  
  
namespace asio = boost::asio;  
  
int main()  
{  
	using Streambuf_t = beast::flat_buffer;  
	using SharedStreambuf_t = std::shared_ptr<Streambuf_t>;  
	using Response_t = beast::http::response<beast::http::dynamic_body>;  
	using SharedResponse_t = std::shared_ptr<Response_t>;  
	using Request_t = beast::http::request<beast::http::string_body>;  
	using SharedRequest_t = std::shared_ptr<Request_t>;  
	using HttpResponseCallback_t = std::function<void(Streambuf_t&, Response_t&)>;  
  
  
	asio::io_service m_IoService;  
	std::thread *m_IoThread = nullptr;  
	asio::ssl::context m_SslContext{ asio::ssl::context::sslv23 };  
	asio::ssl::stream<asio::ip::tcp::socket> m_HttpsStream{ m_IoService, m_SslContext };  
	asio::ssl::stream<asio::ip::tcp::socket> m_WssStream{ m_IoService, m_SslContext };  
	beast::websocket::stream<decltype(m_WssStream)&> m_WebSocket{ m_WssStream };  
  
  
  
	asio::ip::tcp::resolver r{ m_IoService };  
	boost::system::error_code error;  
	auto target = r.resolve({ "discordapp.com", "https" }, error);  
	if (error)  
	{  
		return false;  
	}  
  
	error.clear();  
	asio::connect(m_HttpsStream.lowest_layer(), target, error);  
	if (error)  
	{  
		return false;  
	}  
  
	// SSL handshake  
	error.clear();  
	m_HttpsStream.set_verify_mode(asio::ssl::verify_none); // TODO error check  
	m_HttpsStream.handshake(asio::ssl::stream_base::client, error);  
	if (error)  
	{  
		return false;  
	}  
  
  
  
	auto req = std::make_shared<Request_t>();  
	req->method(beast::http::verb::post);  
	req->target("/api/v5/channels/243719144292220928/messages");  
	req->version = 11;  
	req->insert("Host", "discordapp.com");  
	req->insert("User-Agent", "DiscordBot (github.com/maddinat0r/samp-discord-connector, v0.1)");  
		req->insert("Content-Type", "application/json");  
	req->insert("Authorization", "Bot MjQzNzk5Mjg3MTg2NzE4NzIw.CwCSWA.EPbEmgYxlm50AzekELq-4sBbbH0");  
	req->body = "{\"content\":\"LOL hi #1\"}";  
  
	req->prepare();  
  
  
	m_IoService.dispatch([&, req]() mutable  
	{  
		boost::system::error_code error_code;  
		beast::http::write(m_HttpsStream, *req, error_code);  
		if (error_code)  
		{  
			return;  
		}  
  
		error_code.clear();  
  
		Streambuf_t sb;  
		Response_t response;  
		beast::http::read(m_HttpsStream, sb, response, error_code);  
		if (error_code)  
		{  
			// error here: bad chunk (17)  
			std::cout << error_code.message();  
			return;  
		}  
	});  
  
	m_IoThread = new std::thread([&]()  
	{  
		m_IoService.run();  
	});  
  
	std::cin.get();  
	return 0;  
}  
```  
  
### All relevant compiler information  
MSVC 14.1 (Visual Studio 2017), x86, Debug and Release configurations  
Boost 1.64.0  
OpenSSL 1.1.0f  
  
### Other  
I've been using Beast 1.0.0-b22 and just recently upgraded directly to v47 first. v47 had that problem too, so I tried out the most recent version, v50, because I thought those chunk fixes could fix my problem too, but they didn't. Problem is, that when reading the response, I get the error `bad chunk` (error code 17).  
I'm really not sure what could cause this, as it is still working with b22.  
  
The code above is broken down and simplified code from [one of my projects](https://github.com/maddinat0r/samp-discord-connector/tree/beast-update) (really messy code though at the moment).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-07 20:49:59 UTC  
> Url: https://github.com/boostorg/beast/issues/431#issuecomment-306920561  

Thanks for the detailed bug report!  
  
I rewrote the **v50** branch to include this: **Fix chunk header parsing** https://github.com/vinniefalco/Beast/commit/c93eac05b7edfef2c35f714df86cacc1f3f92cb1  
  
Can you tell me if you were already on that branch, or if the updated branch resolves your issue?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-07 21:36:49 UTC  
> Url: https://github.com/boostorg/beast/issues/431#issuecomment-306932550  

I added a much stronger test and found a defect, working on a fix...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-07 22:27:00 UTC  
> Url: https://github.com/boostorg/beast/issues/431#issuecomment-306943030  

The issue so far is two-fold  
1. A bug in handling of chunk header after a "need more"  
2. A design problem with read_some and async_read_some interfaces  
  
The first seems to be fixed and there are strong tests now ("bufgrind" and "readgrind") which exercise a message parse at all split points.  
  
The second one I have pushed a fix for, it changes the interface to read_some and async_read_some to simply consume the buffer instead of returning the number of bytes. I hope this is sufficient.

---

## Comment 4

> Username: maddinat0r  
> Created at: 2017-06-08 07:02:48 UTC  
> Url: https://github.com/boostorg/beast/issues/431#issuecomment-307017493  

Those changes fixed my issue. Thanks!
