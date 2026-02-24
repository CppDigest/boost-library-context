# #1087 - websocket streaming to multiple clients not working [Closed]

> Username: clartillot  
> Created at: 2018-04-03 09:41:46 UTC  
> Updated at: 2022-12-30 10:38:34 UTC  
> Closed at: 2018-04-04 13:51:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1087  

BOOST_BEAST_VERSION 144  
(boost 1.66.0)  
  
Hello,  
  
I started from websocket_server_async.   
I have added a timer (similar to advanced_server's) in order to periodically stream a new message to all clients connected to the same websocket instance on the server (same port).  
But when several clients (javascript) are connected to the same websocket, only one client will receive the message from the server. For each new sent message, the client that will receive it seems to be selected randomly (and can change each time). It looks the same whatever client navigator I use (chrome ou firefox).  
Note : when I use a different websocket instance for each client (different port on the server), the issue dos not happen, of course.  
I send you my code sample next. Thanks a lot for your help.  
  
```  
#define _WIN32_WINNT 0x0501  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/io_service.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/asio/bind_executor.hpp>  
#include <boost/asio/signal_set.hpp>  
#include <boost/asio/steady_timer.hpp>  
#include <ostream>  
#include <thread>  
#include "WebSocketServer.h"  
  
using tcp = boost::asio::ip::tcp;               // from <boost/asio/ip/tcp.hpp>  
namespace websocket = boost::beast::websocket;  // from <boost/beast/websocket.hpp>  
  
// Report a failure  
void  
fail(boost::system::error_code ec, char const* what)  
{  
	std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Echoes back all received WebSocket messages  
class session : public std::enable_shared_from_this<session>  
{  
	websocket::stream<tcp::socket> ws_;  
	boost::asio::strand<  
		boost::asio::io_context::executor_type> strand_;  
	boost::asio::steady_timer timer_;  
	boost::beast::multi_buffer bufferRead_;  
	boost::beast::multi_buffer bufferWrite_;  
	WebSocketServer::PWSDATA threadParam_;  
  
public:  
	// Take ownership of the socket  
	explicit  
		session(tcp::socket socket, WebSocketServer::PWSDATA threadParam)  
		: ws_(std::move(socket))  
		, strand_(ws_.get_executor())  
		, threadParam_(threadParam)  
		, timer_(ws_.get_executor().context(),  
		(std::chrono::steady_clock::time_point::max)())  
	{  
	}  
  
	// Start the asynchronous operation  
	void  
		run()  
	{  
		// Run the timer. The timer is operated  
		// continuously, this simplifies the code.  
		on_timer({});  
  
		// Set the timer  
		timer_.expires_after(std::chrono::milliseconds(10));  
  
		// Accept the websocket handshake  
		ws_.async_accept(  
			boost::asio::bind_executor(  
				strand_,  
				std::bind(  
					&session::on_accept,  
					shared_from_this(),  
					std::placeholders::_1)));  
	}  
  
	void  
		on_accept(boost::system::error_code ec)  
	{  
		if (ec)  
			return fail(ec, "accept");  
  
		if (!threadParam_->isPushMode) {  
			// PULL mode : read first message  
			do_read();  
		}  
	}  
  
	// Called when the timer expires.  
	void  
		on_timer(boost::system::error_code ec)  
	{  
		if (ec && ec != boost::asio::error::operation_aborted)  
			return fail(ec, "timer");  
  
		// See if the timer really expired since the deadline may have moved.  
		if (timer_.expiry() <= std::chrono::steady_clock::now())  
		{  
			threadParam_->mutex_ptr->lock();  
			bool isNotifyTreadStop = threadParam_->notifyTreadStop;  
			threadParam_->mutex_ptr->unlock();  
			if (isNotifyTreadStop) {  
				// Closing the socket cancels all outstanding operations. They  
				// will complete with boost::asio::error::operation_aborted  
				ws_.next_layer().shutdown(tcp::socket::shutdown_both, ec);  
				ws_.next_layer().close(ec);  
				return;  
			}  
			else {  
				// Set the timer  
				timer_.expires_after(std::chrono::milliseconds(10));  
  
				do_write();  
			}  
		}  
  
		// Wait on the timer  
		timer_.async_wait(  
			boost::asio::bind_executor(  
				strand_,  
				std::bind(  
					&session::on_timer,  
					shared_from_this(),  
					std::placeholders::_1)));  
	}  
  
	void  
		do_write()  
	{  
		threadParam_->mutex_ptr->lock();  
		bool isSendMsg = !threadParam_->notifyTreadStop && threadParam_->notifyNewSendMessage;  
		if (isSendMsg) {  
			ostream(bufferWrite_) << threadParam_->sendMsg;  
			ws_.text(ws_.got_text());  
			ws_.async_write(  
				bufferWrite_.data(),  
				boost::asio::bind_executor(  
					strand_,  
					std::bind(  
						&session::on_write,  
						shared_from_this(),  
						std::placeholders::_1,  
						std::placeholders::_2)));  
			threadParam_->notifyNewSendMessage = false;  
		}  
		threadParam_->mutex_ptr->unlock();  
	}  
  
	void  
		on_write(  
			boost::system::error_code ec,  
			std::size_t bytes_transferred)  
	{  
		boost::ignore_unused(bytes_transferred);  
  
		// This indicates that the session was closed  
		if (ec == websocket::error::closed)  
			return;  
  
		if (ec)  
			return fail(ec, "write");  
  
		// Clear the buffer  
		bufferWrite_.consume(bufferWrite_.size());  
	}  
  
	void  
		do_read()  
	{  
		threadParam_->mutex_ptr->lock();  
		bool isNotifyTreadStop = threadParam_->notifyTreadStop;  
		threadParam_->mutex_ptr->unlock();  
		if (!isNotifyTreadStop) {  
			// Read a message into our buffer  
			ws_.async_read(  
				bufferRead_,  
				boost::asio::bind_executor(  
					strand_,  
					std::bind(  
						&session::on_read,  
						shared_from_this(),  
						std::placeholders::_1,  
						std::placeholders::_2)));  
		}  
	}  
  
	void  
		on_read(  
			boost::system::error_code ec,  
			std::size_t bytes_transferred)  
	{  
		boost::ignore_unused(bytes_transferred);  
  
		// This indicates that the session was closed  
		if (ec == websocket::error::closed)  
			return;  
  
		if (ec)  
			fail(ec, "read");  
  
		// store the message for subsequent processing  
		threadParam_->mutex_ptr->lock();  
		if (!threadParam_->notifyNewReceiveMessage && bufferRead_.size() > 0) {  
			std::ostringstream stream;  
			stream << buffers(bufferRead_.data());  
			threadParam_->receiveMsg.assign(stream.str());  
			threadParam_->notifyNewReceiveMessage = true;  
		}  
		bufferRead_.consume(bufferRead_.size());  
		threadParam_->mutex_ptr->unlock();  
  
		if (!threadParam_->isPushMode) {  
			// PULL mode : read next message  
			do_read();  
		}  
	}  
  
};  
  
//------------------------------------------------------------------------------  
  
// Accepts incoming connections and launches the sessions  
class listener : public std::enable_shared_from_this<listener>  
{  
	tcp::acceptor acceptor_;  
	tcp::socket socket_;  
	WebSocketServer::PWSDATA threadParam_;  
  
public:  
	listener(  
		boost::asio::io_context& ioc,  
		tcp::endpoint endpoint,  
		WebSocketServer::PWSDATA threadParam)  
		: acceptor_(ioc)  
		, socket_(ioc)  
		, threadParam_(threadParam)  
	{  
		boost::system::error_code ec;  
  
		// Open the acceptor  
		acceptor_.open(endpoint.protocol(), ec);  
		if (ec)  
		{  
			fail(ec, "open");  
			return;  
		}  
  
		// Allow address reuse  
		acceptor_.set_option(boost::asio::socket_base::reuse_address(true));  
		if (ec)  
		{  
			fail(ec, "set_option");  
			return;  
		}  
  
		// Bind to the server address  
		acceptor_.bind(endpoint, ec);  
		if (ec)  
		{  
			fail(ec, "bind");  
			return;  
		}  
  
		// Start listening for connections  
		acceptor_.listen(  
			boost::asio::socket_base::max_listen_connections, ec);  
		if (ec)  
		{  
			fail(ec, "listen");  
			return;  
		}  
	}  
  
	// Start accepting incoming connections  
	void  
		run()  
	{  
		if (!acceptor_.is_open())  
			return;  
		do_accept();  
	}  
  
	void  
		do_accept()  
	{  
		acceptor_.async_accept(  
			socket_,  
			std::bind(  
				&listener::on_accept,  
				shared_from_this(),  
				std::placeholders::_1));  
	}  
  
	void  
		on_accept(boost::system::error_code ec)  
	{  
		if (ec)  
		{  
			fail(ec, "accept");  
		}  
		else  
		{  
			// Create the session and run it  
			std::make_shared<session>(std::move(socket_), threadParam_)->run();  
		}  
  
		// Accept another connection  
		do_accept();  
	}  
};  
  
DWORD WINAPI WebsocketServerThread(LPVOID lpParam)  
{  
	WebSocketServer::PWSDATA threadParam = (WebSocketServer::PWSDATA)lpParam;  
  
	try  
	{  
		// initialise param  
		auto const address = boost::asio::ip::make_address(threadParam->address);  
		auto const port = static_cast<unsigned short>(std::atoi(threadParam->port.c_str()));  
		auto const nbThreads = 1;  
  
		boost::asio::io_context _ioc{ nbThreads };  
  
		// Create and launch a listening port  
		std::make_shared<listener>(_ioc, tcp::endpoint{ address, port }, threadParam)->run();  
  
		_ioc.run();  
	}  
	catch (const std::exception& e)  
	{  
		std::cerr << "Error: " << e.what() << std::endl;  
		return EXIT_FAILURE;  
	}  
  
	return 0;  
}  
  
WebSocketServer::WebSocketServer(const std::string address, const std::string port, bool isPushMode)  
{  
	_isServerStarted = false;  
	_address.assign(address);  
	_port.assign(port);  
	_isPushMode = isPushMode;  
}  
  
  
WebSocketServer::~WebSocketServer()  
{  
	// force stop thread if running  
	stopServer();  
}  
  
bool WebSocketServer::isServerStarted()  
{  
	return _isServerStarted;  
}  
  
void WebSocketServer::startServer()  
{  
	if (!_isServerStarted)  
	{  
		_threadParam = (PWSDATA)HeapAlloc(GetProcessHeap(), HEAP_ZERO_MEMORY, sizeof(WSDATA));  
  
		if (_threadParam == NULL)  
			return;  
  
		// initialise param  
		_threadParam->notifyTreadStop = false;  
		_threadParam->notifyNewSendMessage = false;  
		_threadParam->notifyNewReceiveMessage = false;  
		_threadParam->isPushMode = _isPushMode;  
		_threadParam->address.assign(_address);  
		_threadParam->port.assign(_port);  
		_threadParam->mutex_ptr = &_mutex;  
  
		_thread = CreateThread(  
			NULL,                   // default security attributes  
			0,                      // use default stack size    
			WebsocketServerThread,			// thread function name  
			_threadParam,			// argument to thread function   
			0,                      // use default creation flags   
			&_treadId);				// returns the thread identifier   
  
		if (_thread != NULL)  
			_isServerStarted = true;  
	}  
}  
  
void WebSocketServer::stopServer()  
{  
	if (_isServerStarted)  
	{  
		_threadParam->mutex_ptr->lock();  
		_threadParam->notifyTreadStop = true;   
		_threadParam->mutex_ptr->unlock();  
  
		WaitForSingleObject(_thread, INFINITE);  
		CloseHandle(_thread);  
		if (_threadParam != NULL)  
		{  
			HeapFree(GetProcessHeap(), 0, _threadParam);  
			_threadParam = NULL;  
		}  
		_isServerStarted = false;  
	}  
}  
  
void WebSocketServer::sendMessageToClients(const std::string msg)  
{  
	if (_isServerStarted) {  
		_threadParam->mutex_ptr->lock();  
		if (!_threadParam->notifyNewSendMessage)  
		{  
			_threadParam->sendMsg.clear();  
			_threadParam->sendMsg.assign(msg);  
			_threadParam->notifyNewSendMessage = true;  
		}  
		_threadParam->mutex_ptr->unlock();  
	}  
}  
  
bool WebSocketServer::getMessageFromClient(std::string& msg)  
{  
	bool ret = false;  
	if (_isServerStarted) {  
		_threadParam->mutex_ptr->lock();  
		if (_threadParam->notifyNewReceiveMessage)  
		{  
			msg.assign(_threadParam->receiveMsg);  
			_threadParam->receiveMsg.clear();  
			_threadParam->notifyNewReceiveMessage = false;  
			ret = true;  
		}  
		_threadParam->mutex_ptr->unlock();  
	}  
	return ret;  
}  
```

---

## Comment 1

> Username: djarek  
> Created at: 2018-04-03 09:56:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1087#issuecomment-378195638  

Note for the future: please put code snippets at least into \`\`\`e.g.  
\`\`\`cpp  
int foo();  
\`\`\`  
  
which turns into  
  
```cpp  
int foo();  
```

---

## Comment 2

> Username: clartillot  
> Created at: 2018-04-03 10:00:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1087#issuecomment-378196633  

sorry for that !

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-03 11:16:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1087#issuecomment-378214915  

Well, the way that you are "broadcasting" the message is not correct. You are setting a flag, which is only checked when an outbound message is sent. And then you clear the flag. The behavior you describe, a seemingly random client gets the message and no one else, is exactly what you have coded.  
  
You need to get rid of this `_threadParam` and `mutex_ptr` and replace it with a list of connections. To broadcast,, add a queue to each connection and use `boost::asio::post` on each connection to invoke a lambda on the connection's strand which adds the  message to broadcast to the connection's queue. Then, either process the queue if no write is outstanding (use a flag for this), or when the next write completes send the next item in the queue.

---

## Comment 4

> Username: clartillot  
> Created at: 2018-04-04 13:51:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1087#issuecomment-378606885  

Thanks a lot. I have just achieved to make it work correctly, after rewriting my code using your tips (and a deeper study of asio:-)
