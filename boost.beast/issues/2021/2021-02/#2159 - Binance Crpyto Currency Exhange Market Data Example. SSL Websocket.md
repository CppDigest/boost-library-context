# #2159 - Binance Crpyto Currency Exhange Market Data Example. SSL Websocket. [Closed]

> Username: DaManJ  
> Created at: 2021-02-02 12:27:15 UTC  
> Updated at: 2021-02-25 19:11:35 UTC  
> Closed at: 2021-02-25 19:11:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2159  

Hello, first thanks for the library.  
I have written following test code modified from the examples, and helpful information I found on some issue links.  
This test subscribes to bitcoin price on Binance exchange and prints price updates to console.  
It also recovers from network failure (you can unplug / replug network cable).  
  
The async examples look quite complicated to me and with all the chains of callbacks etc seems a bit tricky to implement this case robustly.  
Is this code ok, can you spot any problems? Is there a better way to do this with Async methods?  
  
```  
//  
// Copyright (c) 2016-2019 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
#include "./root_certificates.hpp"  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/websocket/ssl.hpp>  
  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/ssl/stream.hpp>  
  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
  
//Class to connect to a websocket endpoint and receive data continuously counterparty closes or stopped manually  
class WebSocketSession : public std::enable_shared_from_this<WebSocketSession>  
{  
	net::io_context & _ioContext;  
	ssl::context& _sslContext;  
	tcp::resolver _tcpResolver;  
	std::shared_ptr<websocket::stream<beast::ssl_stream<tcp::socket>>> _ws;  
	beast::flat_buffer _buffer;  
	std::string _host;  
	std::string _hostAndPort;  
	char const * _port;  
	std::string _target;  
	std::string _text;  
	tcp::endpoint _tcpEndPoint;  
	bool _terminate = false;  
		  
	void ConnectAndReceive()  
	{  
		// Look up the domain name  
		auto const tcpResolverResults = _tcpResolver.resolve(_host, _port);  
  
		// Make the connection on the IP address we get from a lookup  
		_tcpEndPoint = net::connect(get_lowest_layer(*_ws), tcpResolverResults);  
  
		// Set SNI Hostname (many hosts need this to handshake successfully)  
		if (!SSL_set_tlsext_host_name(_ws->next_layer().native_handle(), _host.c_str())) {  
			throw beast::system_error(  
				beast::error_code(static_cast<int>(::ERR_get_error()), net::error::get_ssl_category()), "Failed to set SNI Hostname");  
		}  
		// Update the _host string. This will provide the value of the  
		// Host HTTP header during the WebSocket handshake.  
		// See https://tools.ietf.org/html/rfc7230#section-5.4  
		_hostAndPort = _host + ':' + std::to_string(_tcpEndPoint.port());  
  
		// Perform the SSL handshake  
		_ws->next_layer().handshake(ssl::stream_base::client);  
  
		// Set a decorator to change the User-Agent of the handshake  
		_ws->set_option(websocket::stream_base::decorator(  
			[](websocket::request_type& req)  
			{  
				req.set(http::field::user_agent,  
					std::string(BOOST_BEAST_VERSION_STRING) +  
					" websocket-client-coro");  
			}));  
  
		// Perform the websocket handshake  
		_ws->handshake(_hostAndPort, _target);  
  
		//Read Messages  
		try {  
			while (true) //expect to read continuously until a connection failure  
			{  
				if (!_terminate) {  
					_ws->read(_buffer);  
  
					if (_buffer.size() > 0)  
					{  
						std::cout << beast::make_printable(_buffer.data()) << std::endl;  
					}  
					_buffer.clear();  
				}  
				else {  
					_ws->close(boost::beast::websocket::close_code::normal);  
				}  
  
			}  
		}  
		catch (beast::system_error const& se) {  
			if (se.code() == websocket::error::closed) {  
				std::cout << "socket was closed." << std::endl;  
			}  
			else {  
				std::cout << "exception: " << se.code() << ", " << se.code().message() << ", " << se.what();  
			}  
		}  
		catch (std::exception& ex) {  
			std::cout << "exception: " << ex.what();  
		}  
  
	}  
  
public:  
  
	// Resolver and socket require an io_context  
	explicit  
		WebSocketSession(net::io_context& ioc,  
			ssl::context& ctx,  
			char const* host,  
			char const* port,  
			char const* target)  
		: _ioContext(ioc),  
		_sslContext(ctx),  
		_tcpResolver(_ioContext),  
		_host(host),  
		_port(port),  
		_target(target),  
		_hostAndPort()  
	{  
	}  
  
	void Stop() {  
		_terminate = true;	  
	}  
  
	void Start()  
	{  
		while (!_terminate) {  
  
			_ws = std::make_shared<websocket::stream<beast::ssl_stream<tcp::socket>>>(_ioContext, _sslContext);  
  
			try {  
				ConnectAndReceive(); //initalizes websocket and polls continuously for messages until socket closed or an exception occurs  
			}  
			catch (beast::system_error const& se) {  
				std::cout << "exception: " << se.code() << ", " << se.code().message() << std::endl;  
				Sleep(1000); //just wait a little while to not spam  
			}  
  
			_ws.reset();  
		}  
		  
		//if we get to here process has been terminated  
	}  
  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char** argv)  
{  
	auto host = "stream.binance.com";  
	auto port = "9443";  
	auto target = "/ws/btcusdt@depth@100ms"; //bitcoin full order book endpoint, 100ms updates  
	auto text = "";  
  
	// The io_context is required for all I/O  
	net::io_context ioc;  
	// The SSL context is required, and holds certificates  
	ssl::context ctx{ ssl::context::tlsv12_client };  
	ctx.set_verify_mode(ssl::verify_none);  
	// This holds the root certificate used for verification  
	load_root_certificates(ctx);  
  
	// Run the I/O service. The call will return when  
	// the socket is closed.  
	ioc.run();  
  
	std::string inputStr;  
	std::thread t;  
  
	std::shared_ptr<WebSocketSession> websocketSession;  
  
  
	while (inputStr != "x") {  
		std::cout << "Press 'r' to run. Press 's' to stop. Press 'x' to Exit." << std::endl;  
		std::cin >> inputStr;  
  
		if (inputStr == "r") { //run  
			if (websocketSession.use_count() == 0) { //should not run twice  
				t = std::thread(  
					[&websocketSession, &ioc, &ctx, host, port, target]  
					{  
						websocketSession = std::make_shared<WebSocketSession>(ioc, ctx, host, port, target);  
						websocketSession->Start(); //within a thread so doesn't block  
					});  
			}  
		}  
		else if (inputStr == "s" || inputStr == "x") { //stop & exit  
			if (websocketSession.use_count()) { //running  
				websocketSession->Stop();  
				t.join();  
				t.~thread();  
				websocketSession.reset();  
			}  
			  
		}  
	}  
	  
	return EXIT_SUCCESS;  
}  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-02-02 12:32:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-771603400  

First, I take it this code works?  
  
“better” will depend on your use case. If you have a recent compiler, coroutines can make the code simpler to read (sometimes).  
  
Is there any specific part of your program that concerns you?

---

## Comment 2

> Username: DaManJ  
> Created at: 2021-02-02 12:38:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-771606885  

yes, this code works. I'm using VS2019.  
Nothing in particular concerns me - but i'm new to this library, so maybe it looks like a hack-job to someone with more experience?  
  
Like how I implemented stop, if following is called within stop the the process crashes.  
 _ws->close(boost::beast::websocket::close_code::normal);  
  
So I'm using a boolean there instead which is checked on the read-loop so it can be processed on the same thread - as I read in other issue that the library is not threadsafe when not using async methods.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-02-02 12:42:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-771608784  

Yes the documentation of both beast and asio (upon which it is built) is explicit about io objects not being thread safe.  
If you have more than one thread, you must post() or dispatch() initiating functions onto the correct strand. You must also take care not to initiate two writes at the same time (use a queue).  
  
I have another example you may find instructive (one moment)

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-02-02 12:43:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-771609453  

https://github.com/test-scenarios/boost_beast_websocket_echo  
  
This has both c++20 coroutine examples and c++11

---

## Comment 5

> Username: DaManJ  
> Created at: 2021-02-02 13:15:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-771626811  

Reading the intro on your link, i guess i have avoided most of the asio complexity with this example then.  
I don't really have time to become an asio expert, so maybe better for our production code safety for me to stay away from the async methods for moment! :D   
As long as there is nothing glaringly wrong with my approach.

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-02-02 13:19:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-771628733  

Beast requires to you to be familiar with asio. There is a refresher in the documentation.  
https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_io/asio_refresher.html

---

## Comment 7

> Username: DaManJ  
> Created at: 2021-02-02 13:25:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-771632539  

> Asio is hard to learn because its documentation expects a certain level of knowledge in computer science, asynchronous messaging and underlying network IO. I've been using Asio for some time in production projects. My first attempts were wrong, because I didn't understand (and didn't bother to read up properly) on the meanings of Execution Context, Implicit Strand, Completion Handler and Completion Token. I just wanted to dive in and make things happen.  
  
That is what i was referring to. In this code I am not using Implicit Strand, Completion Handler and Completion Token.  
As far as Asynchronous messaging & network IO, I have an understanding.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-02-02 21:43:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-772013031  

You can use the synchronous websocket client and not worry about threads and completion handlers:  
https://github.com/boostorg/beast/tree/64d8d8618c34c04bf1ad279fbae72324b36ef3d0/example/websocket/client/sync-ssl

---

## Comment 9

> Username: DaManJ  
> Created at: 2021-02-02 23:26:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-772085648  

hi @vinniefalco yes that is the one I based some code on

---

## Comment 10

> Username: DaManJ  
> Created at: 2021-02-04 01:48:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-772962317  

Extended example with json parser (simdjson required), basic callbacks class/interface, and a simple Exchange class that can handle multiple websockets for simultaneous price subscriptions.  
Subscribes to bitcoin & ethereum prices.  
  
```  
//  
// Copyright (c) 2016-2019 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
#include "./root_certificates.hpp"  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/websocket/ssl.hpp>  
  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/ssl/stream.hpp>  
#include "simdjson/simdjson.h"  
  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
class WebSocketSessionCallbacks  
{  
public:  
	//notifies of a connection failure - socket disconnect, problem passing message, socket closed etc  
	virtual void WebSocketConnectionDown(const std::string & sessionName) = 0;   
  
	//a message received on the socket  
	virtual void WebSocketMessageReceived(const std::string& sessionName, simdjson::simdjson_result<simdjson::dom::element>& jsonResult) = 0;  
};  
  
//Class to connect to a websocket endpoint and receive data continuously counterparty closes or stopped manually  
class WebSocketSession : public std::enable_shared_from_this<WebSocketSession>  
{  
	net::io_context & _ioContext;  
	ssl::context& _sslContext;  
	tcp::resolver _tcpResolver;  
	std::shared_ptr<websocket::stream<beast::ssl_stream<tcp::socket>>> _ws;  
	beast::flat_buffer _buffer;  
	std::string _host;  
	std::string _hostAndPort;  
	char const * _port;  
	std::string _target;  
	std::string _text;  
	tcp::endpoint _tcpEndPoint;  
	bool _terminate = false;  
	simdjson::dom::parser _jsonParser;  
	WebSocketSessionCallbacks & _callbacks;  
	std::thread _workerThread;  
	std::string _sessionIdenfitier;  
		  
	void ConnectAndReceive()  
	{  
		// Look up the domain name  
		auto const tcpResolverResults = _tcpResolver.resolve(_host, _port);  
  
		// Make the connection on the IP address we get from a lookup  
		_tcpEndPoint = net::connect(get_lowest_layer(*_ws), tcpResolverResults);  
  
		// Set SNI Hostname (many hosts need this to handshake successfully)  
		if (!SSL_set_tlsext_host_name(_ws->next_layer().native_handle(), _host.c_str())) {  
			throw beast::system_error(  
				beast::error_code(static_cast<int>(::ERR_get_error()), net::error::get_ssl_category()), "Failed to set SNI Hostname");  
		}  
		// Update the _host string. This will provide the value of the  
		// Host HTTP header during the WebSocket handshake.  
		// See https://tools.ietf.org/html/rfc7230#section-5.4  
		_hostAndPort = _host + ':' + std::to_string(_tcpEndPoint.port());  
  
		// Perform the SSL handshake  
		_ws->next_layer().handshake(ssl::stream_base::client);  
  
		// Set a decorator to change the User-Agent of the handshake  
		_ws->set_option(websocket::stream_base::decorator(  
			[](websocket::request_type& req)  
			{  
				req.set(http::field::user_agent,  
					std::string(BOOST_BEAST_VERSION_STRING) +  
					" websocket-client-coro");  
			}));  
  
		// Perform the websocket handshake  
		_ws->handshake(_hostAndPort, _target);  
  
		//Read Messages  
		try {  
			bool terminating = false;  
			while (true) //expect to read continuously until a connection failure or manually terminated  
			{  
				if (!_terminate || terminating) {  
					_ws->read(_buffer);  
  
					if (_buffer.size() > 0)  
					{  
						std::string bufferString = boost::beast::buffers_to_string(_buffer.data());  
						auto jsonResult = _jsonParser.parse(bufferString);  
  
						_callbacks.WebSocketMessageReceived(_sessionIdenfitier, jsonResult);  
  
					}  
					_buffer.clear();  
				}  
				else { //termination has been requested  
					_ws->close(boost::beast::websocket::close_code::normal);  
					terminating = true; //we could break out of while here, but then we would not read any final message from cpty after we send close?  
				}  
			}  
		}  
		catch (beast::system_error const& se) {  
			if (se.code() == websocket::error::closed) {  
				std::cout << "socket was closed." << std::endl;  
			}  
			else {  
				std::cout << "exception: " << se.code() << ", " << se.code().message() << ", " << std::endl;  
			}  
		}  
		catch (std::exception& ex) {  
			std::cout << "exception: " << ex.what() << std::endl;  
		}  
  
		_callbacks.WebSocketConnectionDown(_sessionIdenfitier);  
	}  
  
public:  
  
	// Resolver and socket require an io_context  
	explicit  
		WebSocketSession(WebSocketSessionCallbacks & callbacks,  
			std::string & sessionIdenfitier,  
			net::io_context& ioc,  
			ssl::context& ctx,  
			char const* host,  
			char const* port,  
			char const* target)  
		:   
		_callbacks(callbacks),  
		_sessionIdenfitier(sessionIdenfitier),  
		_ioContext(ioc),  
		_sslContext(ctx),  
		_tcpResolver(_ioContext),  
		_host(host),  
		_port(port),  
		_target(target),  
		_hostAndPort()  
	{  
	}  
  
	void Stop() {  
		_terminate = true;  
		_workerThread.join();  
		_workerThread.~thread();  
	}  
  
	//Connect to WebSocket on a new thread and start receiving data  
	void Start()  
	{  
		_workerThread = std::thread(  
			[this]  
			{  
				while (!_terminate) {  
  
					_ws = std::make_shared<websocket::stream<beast::ssl_stream<tcp::socket>>>(_ioContext, _sslContext);  
  
					try {  
						//initalizes websocket and polls continuously for messages until socket closed or an exception occurs  
						ConnectAndReceive();  
					}  
					//catches network or ssl handshake errors in attemting to establish the websocket  
					catch (beast::system_error const& se) {  
  
						_callbacks.WebSocketConnectionDown(_sessionIdenfitier); //notify failed connection  
  
						std::cout << "exception: " << se.code() << ", " << se.code().message() << std::endl;  
						Sleep(1000); //just wait a little while to not spam  
					}  
  
					_ws.reset();  
				}  
				//if we get to here Stop has been Called  
				std::cout << "WebSocketSession Stopped." << std::endl;  
			});  
	}  
  
};  
  
//------------------------------------------------------------------------------  
class BinanceExchange : public WebSocketSessionCallbacks  
{  
	const char * _host = "stream.binance.com";  
	const char * _port = "9443";  
  
	net::io_context& _ioc;  
	ssl::context& _ctx;  
	std::map<std::string, std::shared_ptr<WebSocketSession>> _cryptoCurrencySubscriptions;  
	  
	//implement useful application logic with the websocket callbacks ->  
  
	//notifies of a connection failure - socket disconnect, problem passing message, socket closed etc  
	virtual void WebSocketConnectionDown(const std::string& sessionName) override  
	{  
		std::cout << sessionName << ": " << "Connection is down. Orderbook should be cleared" << std::endl;  
	}  
  
	//a message received on the socket  
	virtual void WebSocketMessageReceived(const std::string& sessionName, simdjson::simdjson_result<simdjson::dom::element>& jsonResult) override  
	{  
		std::cout << sessionName << ": " << jsonResult << std::endl;  
	}  
  
public:  
  
	BinanceExchange(net::io_context& ioc, ssl::context& ctx)  
		: _ioc(ioc),  
		_ctx(ctx)  
	{  
	};  
  
	void SubscribeCryptoCurrency(std::string symbol)  
	{  
		std::string target = "/ws/" + symbol + "@depth@100ms"; //full order book endpoint, 100ms updates  
  
		if (_cryptoCurrencySubscriptions.find(symbol) == _cryptoCurrencySubscriptions.end()) {  
			_cryptoCurrencySubscriptions[symbol] = std::make_shared<WebSocketSession>(*this, symbol, _ioc, _ctx, _host, _port, target.c_str());  
			_cryptoCurrencySubscriptions[symbol]->Start(); //subscribes to market data on a new thread  
		}  
		else {  
			std::cout << "Symbol " << symbol << " already subscribed.";  
		}  
		  
	}  
  
	void UnSubscribeCryptoCurrency(std::string symbol) {  
		if (_cryptoCurrencySubscriptions.find(symbol) != _cryptoCurrencySubscriptions.end()) {  
			_cryptoCurrencySubscriptions[symbol]->Stop();  
			_cryptoCurrencySubscriptions.erase(symbol);  
		}  
		else {  
			std::cout << "Symbol " << symbol << " not found.";  
		}  
	}  
  
};  
  
int main(int argc, char** argv)  
{  
	// The io_context is required for all I/O  
	net::io_context ioc;  
	// The SSL context is required, and holds certificates  
	ssl::context ctx{ ssl::context::tlsv12_client };  
	ctx.set_verify_mode(ssl::verify_none);  
	// This holds the root certificate used for verification  
	load_root_certificates(ctx);  
  
	// Run the I/O service. The call will return when  
	// the socket is closed.  
	ioc.run();  
  
	BinanceExchange binanceExchange(ioc, ctx);  
	  
	std::string inputStr;  
	std::thread t;  
  
	while (inputStr != "x") {  
		std::cout << "Press 'r' to run. Press 's' to stop. Press 'x' to Exit." << std::endl;  
		std::cin >> inputStr;  
  
		if (inputStr == "r") { //run  
			binanceExchange.SubscribeCryptoCurrency("btcusdt");  
			binanceExchange.SubscribeCryptoCurrency("ethusdt");  
		}  
		else if (inputStr == "s" || inputStr == "x") { //stop & exit  
  
			binanceExchange.UnSubscribeCryptoCurrency("btcusdt");  
			binanceExchange.UnSubscribeCryptoCurrency("ethusdt");  
  
		}  
	}  
	  
	return EXIT_SUCCESS;  
}  
```

---

## Comment 11

> Username: vinniefalco  
> Created at: 2021-02-04 02:30:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-772978084  

JSON is in Boost now: https://www.boost.org/doc/libs/develop/libs/json/doc/html/index.html

---

## Comment 12

> Username: DaManJ  
> Created at: 2021-02-04 02:45:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-772984629  

@vinniefalco after some research I choose simdjson because of the performance benchmarks noted here and the fact this will be going into a trading application https://github.com/simdjson/simdjson  
  
My knowledge on c++ json libraries is low. I imagine the boost one is appropriate for most scenarios.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2021-02-07 17:10:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-774710733  

> I imagine the boost one is appropriate for most scenarios.  
  
The Boost version is good when you want to modify the JSON or if you want to create a new one. SIMDJson produces a read-only structure, while Boost.JSON produces a modifiable container.

---

## Comment 14

> Username: DaManJ  
> Created at: 2021-02-22 06:23:36 UTC  
> Updated at: 2021-02-22 06:30:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-783120805  

Hi, I have encountered an issue in my code above when trying to close the connection.  
  
On a web-socket where data from server to my client is received irregularly, then I cannot close my application.  
  
As "_ws->read(_buffer)" blocks indefinitely until some data is received on it.  
  
And if I send _ws->close(boost::beast::websocket::close_code::normal) from another thread, then beast crashes.  
  
I cannot pass a timeout to _ws->read as far as I can see.  
  
I am currently looking at setting the idle-timeout, but that does not seem ideal as then the whole websocket would need to be created again each timeout period.  
  
`beast::websocket::stream_base::timeout opt{  
							std::chrono::seconds(20),   // handshake timeout  
							std::chrono::seconds(5),       // idle timeout. no data recv from peer for 5 sec, then it is timeout  
							true   //enable ping-pong to keep alive  
						};  
						_ws->set_option(opt);`  
  
Is there something I am overlooking?  
  
I thought maybe I could intercept the ping/pong handler and send a close from within there, haven't tried that yet but maybe it crashes doing it there as well.  
  
Edit: well setting timout option does nothing anyway, _ws->read still blocks indefinitely.  
  
Thanks

---

## Comment 15

> Username: madmongo1  
> Created at: 2021-02-22 06:31:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-783124770  

If you’re using the async interface it’s pretty straightforward. You can call async_close while there is an async_read outstanding. You must do it on the same thread/strand as the async_read is running on.  
You can achieve this by posting the function which initiates the async_close to the same executor associated with the websocket

---

## Comment 16

> Username: madmongo1  
> Created at: 2021-02-22 06:33:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-783125808  

If you’re using the sync interface it’s not so easy because there’s no way to interrupt a read unless you resort to Closing the underlying socket using OS calls.  
The synchronous interface is designed for simple use cases.

---

## Comment 17

> Username: DaManJ  
> Created at: 2021-02-22 06:39:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-783129078  

Is this an oversight then of beast synch read method?   
Closing a connection is definitely part of simple use-cases. Every workflow should include open and eventually close.  
Having to resort to an OS call to close the connection does not seem like correct way?   
Usually api's with a blocking methods like this can accept a timeout parameter.  
Is my idea to hook into ping/pong worth a try to call close from there?

---

## Comment 18

> Username: madmongo1  
> Created at: 2021-02-22 06:47:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-783133274  

You can certainly add a ping ping but I expect it won’t help. The server will keep responding with pongs even if there’s no price activity.  
Ping pong will detect the case where the connection to the server is interrupted.  
  
most applications that do meaningful IO use async comms. What you could do is set the read timeout on the socket at the OS level.  
  
but before we get into a ping pong of ideas, can you tell me what scenario you’re looking to address?  
  
connection broken, or slow prices?

---

## Comment 19

> Username: DaManJ  
> Created at: 2021-02-22 06:52:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-783135794  

This is just for application shutdown, I need to close the socket so the read method will stop blocking so the worker thread and class can terminate.  
  
https://github.com/binance/binance-spot-api-docs/blob/master/user-data-stream.md  
  
This binance 'user data stream' sends data very irregularly (can be hours or more between anything being sent).

---

## Comment 20

> Username: madmongo1  
> Created at: 2021-02-22 07:25:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-783152811  

OK, normally I would use aync code to in an exchange connector, but in your case sync will work. You just need to reach in and close the socket from under the websocket.  
  
something like:  
```  
::close(ws.next_layer().native_handle());  
```  
  
this will cause the current read to fail and is ok because sockets at the OS level are thread safe.

---

## Comment 21

> Username: DaManJ  
> Created at: 2021-02-22 07:45:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-783164518  

ok thanks for the suggestion, I will give it a try!  
These binance web-sockets are all just receive only - nothing needs to be sent from my client their server over the socket, which is why I opted for simple synch methods for reading.  
For sending data to binance it is all done via HTTP REST requests rather than over websocket.  
  
Thanks

---

## Comment 22

> Username: DaManJ  
> Created at: 2021-02-22 12:33:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-783342195  

Hi, that worked.  
_ws->next_layer().next_layer().close(); for me as my tcp socket is behind ssl layer.  
  
Thanks

---

## Comment 23

> Username: vinniefalco  
> Created at: 2021-02-22 17:00:36 UTC  
> Updated at: 2021-02-22 17:00:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-783520486  

or, `beast::get_lowest_layer(_ws).close()`

---

## Comment 24

> Username: madmongo1  
> Created at: 2021-02-22 17:02:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2159#issuecomment-783521613  

> or, `beast::get_lowest_layer(_ws).close()`  
  
Yes, this is the best solution as it will work regardless of the underlying transport.  
  
Glad you got it solved.
