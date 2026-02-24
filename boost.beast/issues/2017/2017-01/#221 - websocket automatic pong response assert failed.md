# #221 - websocket automatic pong response assert failed [Closed]

> Username: salutant  
> Created at: 2017-01-07 13:06:12 UTC  
> Updated at: 2017-01-10 03:58:04 UTC  
> Closed at: 2017-01-10 03:58:04 UTC  
> Url: https://github.com/boostorg/beast/issues/221  

happened when there has been a write operation already  
```cpp  
     // send pong  
     d.state = do_pong + 1;  
     BOOST_ASSERT(! d.ws.wr_block_);  
     d.ws.wr_block_ = &d;  
     boost::asio::async_write(d.ws.stream_, d.fb.data(), std::move(*this));  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-01-07 13:23:22 UTC  
> Url: https://github.com/boostorg/beast/issues/221#issuecomment-271083600  

I need more information. This assert goes off if you try to do more than one write operation at the same time. Is your code on GitHub?

---

## Comment 2

> Username: salutant  
> Created at: 2017-01-09 11:48:37 UTC  
> Url: https://github.com/boostorg/beast/issues/221#issuecomment-271266022  

the bug does not always happen. so i am write another for test .  
  
server use nodejs with `websocket-node` package   
  
``` javascript  
var WebSocketServer = require('websocket').server;  
var http = require('http');  
  
var server = http.createServer(function(request, response) {  
    console.log((new Date()) + ' Received request for ' + request.url);  
    response.writeHead(404);  
    response.end();  
});  
  
server.listen(88, function() {  
    console.log((new Date()) + ' Server is listening on port 88');  
});  
  
wsServer = new WebSocketServer({ httpServer: server,   
                            fragmentOutgoingMessages: false,  
                            keepalive: false  
                          });  
  
function originIsAllowed(origin) {  
 	return true;  
}  
  
  
wsServer.on('request', function(request) {  
  
    if (!originIsAllowed(request.origin)) {  
      request.reject();  
      return;  
    }  
  
    var connection = request.accept(null, request.origin);  
    var ping_timer = setInterval(function(){  
    	connection.ping("keepalive_ping");   
    }, 5000);  
  
    connection.on("message", function(message){  
    	  
    	if (message.type == "utf8") {  
    		console.log(message.utf8Data);  
    	}  
    });  
  
    connection.on('close', function(reasonCode, description) {  
    	clearInterval(ping_timer);  
    });  
  
});  
```  
  
```cpp  
#define _SCL_SECURE_NO_WARNINGS  
#include <iostream>  
#include <string>  
#include <mutex>  
#include <atomic>  
#include <thread>  
#include <queue>  
#include <boost/asio.hpp>  
#include <boost/asio/steady_timer.hpp>  
#include <beast/websocket.hpp>  
#include <boost/bind.hpp>  
#include <beast/core.hpp>  
  
  
class ws_client  
{  
public:  
	ws_client()  
		: socket_(ios_)  
		, ws_(socket_)  
		, send_pending_(false)  
  
	{}  
  
	~ws_client()  
	{}  
  
	void run(const std::string& host, uint16_t port, const std::string& path)  
	{  
		boost::asio::ip::tcp::endpoint endpoint;  
		endpoint.address(boost::asio::ip::address::from_string(host));  
		endpoint.port(port);  
  
		boost::system::error_code ec;  
  
		socket_.connect(endpoint, ec);  
		if ( ec ) {  
			std::cout << "connect failure" << ec.message() << std::endl;  
			return;  
		}  
  
		ws_.handshake(host, path, ec);  
		if ( ec ) {  
			std::cout << "handshake failure" << ec.message() << std::endl;  
			return;  
		}  
		  
		do_recv();  
  
		std::thread t([this](){  
			this->ios_.run();  
		});  
		t.detach();  
	}  
  
	void send(const std::string& buf)  
	{  
		mtx_.lock();  
		send_queue_.push(buf);  
		mtx_.unlock();  
  
		if ( !send_pending_ ) {  
			do_send();  
		}  
	}  
  
  
private:  
  
	void do_recv()  
	{  
		recvbuf_.consume(recvbuf_.size());  
		ws_.async_read_frame(frame_, recvbuf_, boost::bind(&ws_client::recv_handler, this, boost::asio::placeholders::error));  
	}  
  
	void recv_handler(const boost::system::error_code& ec)  
	{  
		if ( ec ) {  
			std::cout << ec.message() << std::endl;  
			return;  
		}  
  
		std::cout << "RECV:" << beast::to_string(recvbuf_.data()) << std::endl;  
  
		do_recv();  
	}  
  
	void do_send()  
	{  
		send_pending_ = true;  
		sendbuf_.consume(sendbuf_.size());  
		mtx_.lock();  
		if ( !send_queue_.empty() ) {  
			boost::asio::buffer_copy(sendbuf_.prepare(send_queue_.front().size()), boost::asio::buffer(send_queue_.front()));  
			sendbuf_.commit(send_queue_.front().size());  
			send_queue_.pop();  
		}  
		mtx_.unlock();  
  
		if ( sendbuf_.size() > 0 ) {  
			ws_.async_write_frame(true, sendbuf_.data(), boost::bind(&ws_client::send_handler, this, boost::asio::placeholders::error));  
		} else {  
			send_pending_ = false;  
		}  
	}  
  
	void send_handler(const boost::system::error_code& ec)  
	{  
		if ( ec ) {  
			std::cout << ec.message() << std::endl;  
			return;  
		}  
  
		do_send();  
	}  
  
  
private:  
	boost::asio::io_service ios_;  
	boost::asio::ip::tcp::socket socket_;  
	beast::websocket::stream<boost::asio::ip::tcp::socket&> ws_;  
	std::queue<std::string> send_queue_;  
	beast::streambuf sendbuf_;  
	beast::streambuf recvbuf_;  
	beast::websocket::frame_info frame_;  
	  
	std::mutex mtx_;  
	std::atomic_bool send_pending_;  
};  
  
  
  
int main(int argc, char ** argv)  
{  
  
	ws_client client;  
  
	client.run("127.0.0.1", 88, "/");  
  
	for ( ;; ) {  
  
		client.send("send test ..........");  
  
		//std::this_thread::sleep_for(std::chrono::milliseconds(10));  
	}  
  
	return 0;  
}  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-01-09 14:01:36 UTC  
> Updated at: 2017-01-09 14:07:37 UTC  
> Url: https://github.com/boostorg/beast/issues/221#issuecomment-271291158  

Are you calling `ws_client::send` from a foreign thread? In other words, calling `ws_client::send` from a thread that is not associated with the `io_service`? Because the code that you posted is not thread-safe.  
  
You could be executing these two lines of code from different threads at the same time:  
```  
ws_.async_write_frame(true, sendbuf_.data(),  
    boost::bind(&ws_client::send_handler, this, boost::asio::placeholders::error));  
```  
and  
```  
ws_.async_read_frame(frame_, recvbuf_,  
    boost::bind(&ws_client::recv_handler, this, boost::asio::placeholders::error));  
```  
  
I think you also have a race condition with `send_pending_`. You should be checking and setting that `bool` while holding the mutex and adjusting the send queue. It doesn't need to be atomic.  
  
Here's a possible fix (warning: untested)  
```  
	void send(const std::string& buf)  
	{  
                bool call_do_send;  
		mtx_.lock();  
                if(! send_pending_)  
                {  
                        send_pending_ = true  
                        call_do_send = true;  
                }  
                else  
                {  
                        call_do_send = false;  
                }  
		send_queue_.push(buf);  
		mtx_.unlock();  
  
		if (call_do_send)  
		        ws_.get_io_service().post(boost::bind(&ws_client::do_send, this));  
	}  
```

---

## Comment 4

> Username: salutant  
> Created at: 2017-01-10 03:58:04 UTC  
> Url: https://github.com/boostorg/beast/issues/221#issuecomment-271480418  

yeah . that must be a wrong usage of asio . thanks .
