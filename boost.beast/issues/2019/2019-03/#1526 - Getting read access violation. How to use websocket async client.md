# #1526 - Getting read access violation. How to use websocket async client? [Closed]

> Username: mohansella  
> Created at: 2019-03-19 08:51:28 UTC  
> Updated at: 2019-03-19 15:52:11 UTC  
> Closed at: 2019-03-19 15:52:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1526  

Version of Beast : 1.69.0  
OS : Windows 10  
Compiler: VC141 (2017 IDE)  
Flags: Invalid Access happens only in debug mode (always reproducible) but not in release mode (never happened).   
  
I am using beast's websocket as a client with async api. below code are sample codes to demonstrate my issue. The node server does nothing but displaying connected and disconnected message.   
  
Node Server:  
```node  
const WebSocket = require('ws');  
  
var port = 11155;  
  
const wss = new WebSocket.Server({ port: port });  
  
console.log('websocket server listening on %s', port);  
  
wss.on('connection', function connection(ws, req) {  
	console.log('connected to %s', req.connection.remoteAddress);  
	ws.on('message', function incoming(message) {  
		console.log('received: %s from %s', message);  
	});  
	  
	ws.onclose = function(evt) {  
		console.log('closed from %s. reason:%s code:%s wasClean:%s',   
		req.connection.remoteAddress, evt.code, evt.reason, evt.wasClean);  
  }  
});  
```  
  
Native Client  
```cpp  
  
  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/websocket/stream.hpp>  
  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
class WebSocket  
{  
  
	const std::string hostName;  
	const std::string hostPort;  
	const std::string socketPath;  
	  
	net::io_context ioContext;  
	std::unique_ptr<std::thread> thread;  
	std::unique_ptr<tcp::resolver> tcpResolver;  
	std::unique_ptr<websocket::stream<tcp::socket>> webSocket;  
	boost::beast::multi_buffer multiBuffer;  
  
public:  
	WebSocket(std::string & hostName, std::string & hostPort, std::string & socketPath)  
		: hostName(hostName), hostPort(hostPort), socketPath(socketPath)  
	{  
		this->tcpResolver = std::make_unique<tcp::resolver>(  
			this->ioContext  
		);  
		this->webSocket = std::make_unique<websocket::stream<tcp::socket>>(  
			this->ioContext  
		);  
  
		this->tcpResolver->async_resolve(  
			this->hostName, this->hostPort,  
			std::bind(&WebSocket::onResolve, this, std::placeholders::_1, std::placeholders::_2)  
		);  
  
		this->thread = std::make_unique<std::thread>(std::bind(&WebSocket::run, this));  
	}  
  
	~WebSocket()  
	{  
		this->close();  
	}  
  
	void run()  
	{  
		this->ioContext.run();  
	}  
  
	void close()  
	{  
		this->ioContext.stop();  
		this->thread->join();  
		this->tcpResolver.reset();  
		this->webSocket.reset();  
	}  
  
	void reportFailed(boost::system::error_code ec, char const* what)  
	{  
		std::cerr << "websocket failed." << what << ": " << ec.message() << "\n";  
	}  
  
	void onResolve(boost::system::error_code ec, tcp::resolver::results_type results)  
	{  
		if (ec)  
			return reportFailed(ec, __FUNCTION__);  
  
		printf("resolve complete. initiating connection to one of the %zd endpoints found\n", results.size());  
		boost::asio::async_connect(  
			this->webSocket->next_layer(),  
			results.begin(),  
			results.end(),  
			std::bind(&WebSocket::onConnect,this, std::placeholders::_1, std::placeholders::_2)  
		);  
	}  
  
	void onConnect(boost::system::error_code ec, boost::asio::ip::tcp::resolver::iterator it)  
	{  
		if (ec)  
			return reportFailed(ec, __FUNCTION__);  
  
		printf("connected to address:%s port:%d. initiating handshake\n", it->endpoint().address().to_string().c_str(), it->endpoint().port());  
		this->webSocket->async_handshake(  
			this->hostName, this->socketPath,  
			std::bind(&WebSocket::onHandshake, this, std::placeholders::_1)  
		);  
	}  
  
	void onHandshake(boost::system::error_code ec)  
	{  
		if (ec)  
			return reportFailed(ec, __FUNCTION__);  
  
		printf("handshake complete. initiating read\n");  
		this->webSocket->async_read(  
			this->multiBuffer,  
			std::bind(&WebSocket::onRead, this, std::placeholders::_1, std::placeholders::_2)  
		);  
	}  
  
	void onRead(boost::system::error_code ec, std::size_t bytes_transferred)  
	{  
		if (ec)  
			return reportFailed(ec, __FUNCTION__);  
  
		printf("read %zd bytes\n", bytes_transferred);  
	}  
  
};  
  
  
int main()  
{  
  
	std::string hostName = "localhost";  
	std::string hostPort = "11155";  
	std::string socketPath = "/";  
  
	//hostPort = "9909"; socketPath = "/externalbus/websocket"  
  
	//unconditional block to invoke destructor  
	{  
		WebSocket socket(hostName, hostPort, socketPath);  
		getchar();  
		printf("closing...\n");  
	}  
  
	printf("complete. enter to close\n");  
	getchar();  
}  
```  
  
Exception Message:  
  
```  
Exception thrown: read access violation.  
this was 0xFFFFFFFFFFFFFFE7.  
```  
  
Stack trace:  
  
```  
Not Flagged	>	684	Main Thread	Main Thread	EmptyCppProject.exe!boost::asio::execution_context::service::context  
EmptyCppProject.exe!boost::asio::execution_context::service::context() Line 101  
EmptyCppProject.exe!boost::asio::io_context::service::get_io_context() Line 333  
EmptyCppProject.exe!boost::asio::basic_io_object<boost::asio::detail::win_iocp_socket_service<boost::asio::ip::tcp>,1>::get_executor() Line 219  
EmptyCppProject.exe!boost::asio::basic_socket<boost::asio::ip::tcp>::get_executor() Line 279  
EmptyCppProject.exe!boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::get_executor() Line 288  
EmptyCppProject.exe!boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type,boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >,std::_Binder<std::_Unforced,void (__cdecl WebSocket::*)(boost::system::error_code,unsigned __int64),WebSocket *,std::_Ph<1> const &,std::_Ph<2> const &> > >::get_executor() Line 132  
EmptyCppProject.exe!boost::asio::detail::associated_executor_impl<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type,boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >,std::_Binder<std::_Unforced,void (__cdecl WebSocket::*)(boost::system::error_code,unsigned __int64),WebSocket *,std::_Ph<1> const &,std::_Ph<2> const &> > >,boost::asio::system_executor,void>::get(const boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type,boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >,std::_Binder<std::_Unforced,void (__cdecl WebSocket::*)(boost::system::error_code,unsigned __int64),WebSocket *,std::_Ph<1> const &,std::_Ph<2> const &> > > & t, const boost::asio::system_executor & __formal) Line 54  
EmptyCppProject.exe!boost::asio::associated_executor<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type,boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >,std::_Binder<std::_Unforced,void (__cdecl WebSocket::*)(boost::system::error_code,unsigned __int64),WebSocket *,std::_Ph<1> const &,std::_Ph<2> const &> > >,boost::asio::system_executor>::get(const boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type,boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >,std::_Binder<std::_Unforced,void (__cdecl WebSocket::*)(boost::system::error_code,unsigned __int64),WebSocket *,std::_Ph<1> const &,std::_Ph<2> const &> > > & t, const boost::asio::system_executor & ex) Line 95  
EmptyCppProject.exe!boost::asio::detail::handler_work<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type,boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >,std::_Binder<std::_Unforced,void (__cdecl WebSocket::*)(boost::system::error_code,unsigned __int64),WebSocket *,std::_Ph<1> const &,std::_Ph<2> const &> > >,boost::asio::io_context::executor_type>::handler_work<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type,boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >,std::_Binder<std::_Unforced,void (__cdecl WebSocket::*)(boost::system::error_code,unsigned __int64),WebSocket *,std::_Ph<1> const &,std::_Ph<2> const &> > >,boost::asio::io_context::executor_type>(boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type,boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >,std::_Binder<std::_Unforced,void (__cdecl WebSocket::*)(boost::system::error_code,unsigned __int64),WebSocket *,std::_Ph<1> const &,std::_Ph<2> const &> > > & handler) Line 37  
EmptyCppProject.exe!boost::asio::detail::win_iocp_socket_recv_op<std::array<boost::asio::mutable_buffer,2>,boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_some_op<boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type,boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>,1>::read_op<boost::beast::basic_multi_buffer<std::allocator<char> >,std::_Binder<std::_Unforced,void (__cdecl WebSocket::*)(boost::system::error_code,unsigned __int64),WebSocket *,std::_Ph<1> const &,std::_Ph<2> const &> > > >::do_complete(void * owner, boost::asio::detail::win_iocp_operation * base, const boost::system::error_code & result_ec, unsigned __int64 bytes_transferred) Line 65  
EmptyCppProject.exe!boost::asio::detail::win_iocp_operation::destroy() Line 53  
EmptyCppProject.exe!boost::asio::detail::win_iocp_io_context::shutdown() Line 125  
EmptyCppProject.exe!boost::asio::detail::service_registry::shutdown_services() Line 45  
EmptyCppProject.exe!boost::asio::execution_context::shutdown() Line 42  
EmptyCppProject.exe!boost::asio::execution_context::~execution_context() Line 34  
EmptyCppProject.exe!boost::asio::io_context::~io_context() Line 57  
EmptyCppProject.exe!WebSocket::~WebSocket() Line 58  
EmptyCppProject.exe!main() Line 143  
[External Code]  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-19 14:01:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1526#issuecomment-474383383  

Can you please try it with the Boost 1.70 beta? This might be a bug in the library that was fixed recently.

---

## Comment 2

> Username: mohansella  
> Created at: 2019-03-19 14:30:33 UTC  
> Updated at: 2019-03-19 15:52:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1526#issuecomment-474396210  

~~tried now with Boost 1.70.b1 still the same.~~  
It is working fine with Beta.
