# #2731 - Not to send FIN+ACK on `boost::asio::async_write` [Closed]

> Username: MortezaBashsiz  
> Created at: 2023-08-30 08:17:07 UTC  
> Updated at: 2023-08-30 11:17:53 UTC  
> Closed at: 2023-08-30 11:17:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2731  

What I am doing is  
Receive a connect request from client and send it from an agent to a server (both agent and server written by boost libraries) and get the response `HTTP/1.1 200 Connection established\r\n` from server. Like an HTTPS proxy.  
  
client use http_proxy and sends to agent ---request---> agent (encrypt client request to a new POST request with body and sends it to server) ---request---> server (decrypt body finds original request from client sends request to original destination) ---request---> original destination ---response---> server (encrypt and return to agent as response) ---response---> agent (decrypt body and send response to client) ---response---> client  
  
But when I want to test it, I see a FIN+ACK from agent to client that should not be there  
  
Is there any way to configure the socket not to send the FIN+ACK while using `boost::asio::async_write` ?  
  
Definition of class connection  
```C  
class Connection : public std::enable_shared_from_this<Connection> {  
	public:  
		Connection(const Connection&) = delete;  
		Connection& operator=(const Connection&) = delete;  
		explicit Connection(boost::asio::ip::tcp::socket socket, ConnectionManager& manager, RequestHandler& handler);  
		void start();  
		void stop();  
	  
	private:  
		void doRead();  
		void doWrite();  
		boost::asio::ip::tcp::socket socket_;  
		ConnectionManager& ConnectionManager_;  
		RequestHandler& RequestHandler_;  
		std::array<char, 8192> buffer_;  
		request request_;  
		response response_;  
};  
  
typedef std::shared_ptr<Connection> ConnectionPtr;  
```  
  
And also I have set the keep_alive true for socket in constructor  
```C  
Connection::Connection(boost::asio::ip::tcp::socket socket,  
		ConnectionManager& manager, RequestHandler& handler)  
	: socket_(std::move(socket)),  
		ConnectionManager_(manager),  
		RequestHandler_(handler)  
{  
	boost::asio::socket_base::keep_alive option(true);  
	socket_.set_option(option);  
}  
```  
  
Class connection manager  
```C  
class ConnectionManager  
{  
	public:  
		ConnectionManager(const ConnectionManager&) = delete;  
		ConnectionManager& operator=(const ConnectionManager&) = delete;  
		ConnectionManager(Config config);  
		void start(ConnectionPtr c);  
		void stop(ConnectionPtr c);  
		void stopAll();  
	  
	private:  
		std::set<ConnectionPtr> connections_;  
};  
```  
  
I use the following function to write on socket  
```C  
void Connection::doWrite() {  
	auto self(shared_from_this());  
	boost::asio::async_write(socket_, response_.toBuffers(), [this, self](boost::system::error_code ec, std::size_t) {  
		if (!ec) {  
			boost::system::error_code ignored_ec;  
			socket_.shutdown(boost::asio::ip::tcp::socket::shutdown_both,  
				ignored_ec);  
		}  
		if (ec != boost::asio::error::operation_aborted) {  
			ConnectionManager_.stop(shared_from_this());  
		}  
	});  
}  
```  
Sample tcpdump   
  
![image](https://github.com/boostorg/beast/assets/19620677/f929cbb3-f822-4bce-8487-65c38d3031c1)  
  
My curl output   
```  
[~]>$ curl https://ident.me -v  
* processing: https://ident.me  
* Uses proxy env variable https_proxy == '127.0.0.1:8080'  
*   Trying 127.0.0.1:8080...  
* Connected to 127.0.0.1 (127.0.0.1) port 8080  
* CONNECT tunnel: HTTP/1.1 negotiated  
* allocate connect buffer  
* Establish HTTP proxy tunnel to ident.me:443  
> CONNECT ident.me:443 HTTP/1.1  
> Host: ident.me:443  
> User-Agent: curl/8.2.1  
> Proxy-Connection: Keep-Alive  
>   
< HTTP/1.1 200 Connection established  
<   
* CONNECT phase completed  
* CONNECT tunnel established, response 200  
* ALPN: offers h2,http/1.1  
* TLSv1.3 (OUT), TLS handshake, Client hello (1):  
*  CAfile: /etc/ssl/certs/ca-certificates.crt  
*  CApath: none  
* OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to ident.me:443   
* Closing connection  
curl: (35) OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to ident.me:443   
[~]>$   
  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2023-08-30 09:08:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2731#issuecomment-1698788170  

@MortezaBashsiz do you mean it sends FIN+ACK without closing or shutting down the socket? could you provide a minimal reproducible example?

---

## Comment 2

> Username: MortezaBashsiz  
> Created at: 2023-08-30 09:20:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2731#issuecomment-1698805740  

@ashtum   
TBH I don't know what happens exactly in backend but for sure I commented out all the close and shutdown in my code, but it doesn't make changes, and it sends FIN+ACK in any situation  
Regarding the minimal reproducible example, give me some time I will try to do it

---

## Comment 3

> Username: ashtum  
> Created at: 2023-08-30 09:25:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2731#issuecomment-1698813121  

Which side sends the FIN+ACK? are you sure the socket instance is not falling out of scope or destroyed, because it closes the socket in the destructor.  
I couldn't follow that is going form the provided code and wireshark screenshot, are you receiving client hello after FIN+ACK?  
A minimal reproducible example can help in these situations.

---

## Comment 4

> Username: MortezaBashsiz  
> Created at: 2023-08-30 10:08:09 UTC  
> Updated at: 2023-08-30 10:41:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2731#issuecomment-1698877201  

Here is a minimal reproducible example  
  
You can make it with  
  
```  
g++ main.cpp -o main  
```  
main.cpp  
```C++  
#include <iostream>  
#include <set>  
#include <boost/asio.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/process/async_pipe.hpp>  
#include <boost/lexical_cast.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
  
std::vector<boost::asio::const_buffer> toBuffers(std::string str) {  
	std::vector<boost::asio::const_buffer> buffers;  
	buffers.push_back(boost::asio::buffer(str));  
	const char miscCrlf[] = { '\r', '\n' };  
	buffers.push_back(boost::asio::buffer(miscCrlf));  
	return buffers;  
};  
  
  
class ConnectionManager;  
  
class Connection : public std::enable_shared_from_this<Connection> {  
	public:  
		Connection(const Connection&) = delete;  
		Connection& operator=(const Connection&) = delete;  
		explicit Connection(boost::asio::ip::tcp::socket socket, ConnectionManager& manager);  
		void start();  
		void stop();  
	  
	private:  
		void doRead();  
		void doWrite();  
		boost::asio::ip::tcp::socket socket_;  
		ConnectionManager& ConnectionManager_;  
		std::array<char, 8192> buffer_;  
};  
  
typedef std::shared_ptr<Connection> ConnectionPtr;  
  
class ConnectionManager  
{  
	public:  
		ConnectionManager(const ConnectionManager&) = delete;  
		ConnectionManager& operator=(const ConnectionManager&) = delete;  
		ConnectionManager();  
		void start(ConnectionPtr c);  
		void stop(ConnectionPtr c);  
		void stopAll();  
	  
	private:  
		std::set<ConnectionPtr> connections_;  
};  
  
Connection::Connection(boost::asio::ip::tcp::socket socket,  
		ConnectionManager& manager)  
	: socket_(std::move(socket)),  
		ConnectionManager_(manager)  
{  
	boost::asio::socket_base::keep_alive option(true);  
	socket_.set_option(option);  
}  
  
void Connection::start() {  
	doRead();  
}  
  
void Connection::stop() {  
	socket_.close();  
}  
  
void Connection::doRead() {  
	auto self(shared_from_this());  
	socket_.async_read_some(boost::asio::buffer(buffer_), [this, self](boost::system::error_code ec, std::size_t bytesTransferred) {  
		char data[bytesTransferred];  
		std::memcpy(data, buffer_.data(), bytesTransferred);  
		std::cout << "PACKET : " << std::endl << data << std::endl;  
		if (!ec) {  
			doWrite();  
			doRead();  
		} else if (ec != boost::asio::error::operation_aborted) {  
			ConnectionManager_.stop(shared_from_this());  
		}  
	});  
}  
  
void Connection::doWrite() {  
	auto self(shared_from_this());  
	boost::asio::async_write(socket_, toBuffers("HTTP/1.1 200 Connection established\r\n"), [this, self](boost::system::error_code ec, std::size_t) {  
		if (!ec) {  
			boost::system::error_code ignored_ec;  
			socket_.shutdown(boost::asio::ip::tcp::socket::shutdown_both,  
				ignored_ec);  
		}  
		if (ec != boost::asio::error::operation_aborted) {  
			ConnectionManager_.stop(shared_from_this());  
		}  
	});  
}  
  
ConnectionManager::ConnectionManager(){  
}  
  
void ConnectionManager::start(ConnectionPtr c) {  
	connections_.insert(c);  
	c->start();  
}  
  
void ConnectionManager::stop(ConnectionPtr c) {  
	connections_.erase(c);  
	c->stop();  
}  
  
void ConnectionManager::stopAll() {  
	for (auto c: connections_)  
		c->stop();  
	connections_.clear();  
}  
  
class agent {  
	public:  
		agent(const agent&) = delete;  
		agent& operator=(const agent&) = delete;  
		explicit agent();  
		void run();  
	  
	private:  
		void doAccept();  
		void doAwaitStop();  
		boost::asio::io_context io_context_;  
		boost::asio::signal_set signals_;  
		boost::asio::ip::tcp::acceptor acceptor_;  
		ConnectionManager ConnectionManager_;  
};  
  
agent::agent()  
	: io_context_(1),  
		signals_(io_context_),  
		acceptor_(io_context_),  
		ConnectionManager_(){  
  
	signals_.add(SIGINT);  
	signals_.add(SIGTERM);  
#if defined(SIGQUIT)  
	signals_.add(SIGQUIT);  
#endif // defined(SIGQUIT)  
	doAwaitStop();  
	boost::asio::ip::tcp::resolver resolver(io_context_);  
	boost::asio::ip::tcp::endpoint endpoint = *resolver.resolve("127.0.0.1", "8080").begin();  
	acceptor_.open(endpoint.protocol());  
	acceptor_.set_option(boost::asio::ip::tcp::acceptor::reuse_address(true));  
	acceptor_.bind(endpoint);  
	acceptor_.listen();  
	doAccept();  
};  
  
void agent::run() {  
	io_context_.run();  
};  
  
void agent::doAccept() {  
	acceptor_.async_accept(  
			[this](std::error_code ec, boost::asio::ip::tcp::socket socket)  
			{  
				if (!acceptor_.is_open()) {  
					return;  
				};  
				if (!ec) {  
					ConnectionManager_.start(std::make_shared<Connection>(  
							std::move(socket), ConnectionManager_));  
				};  
				doAccept();  
			});  
}  
  
void agent::doAwaitStop() {  
	signals_.async_wait(  
			[this](std::error_code /*ec*/, int /*signo*/)  
			{  
				acceptor_.close();  
				ConnectionManager_.stopAll();  
			});  
}  
  
int main(int argc=0, char* argv[]=0){  
  
	try {  
		agent nipoAgent;  
		nipoAgent.run();  
	}  
	catch (std::exception& e) {  
		std::cerr << "exception: " << e.what() << "\n";  
		exit(1);  
	}  
	return 0;  
};  
  
```  
  
Then you made it, you can simply run it like  
```  
./main  
```  
  
It will open port 8080 on 127.0.0.1 and you can simply test it with  
```  
[~]>$ export https_proxy="127.0.0.1:8080"  
[~]>$ curl https://ident.me -v  
* processing: https://ident.me  
* Uses proxy env variable https_proxy == '127.0.0.1:8080'  
*   Trying 127.0.0.1:8080...  
* Connected to 127.0.0.1 (127.0.0.1) port 8080  
* CONNECT tunnel: HTTP/1.1 negotiated  
* allocate connect buffer  
* Establish HTTP proxy tunnel to ident.me:443  
> CONNECT ident.me:443 HTTP/1.1  
> Host: ident.me:443  
> User-Agent: curl/8.2.1  
> Proxy-Connection: Keep-Alive  
>   
< HTTP/1.1 200 Connection established  
<   
* CONNECT phase completed  
* CONNECT tunnel established, response 200  
* ALPN: offers h2,http/1.1  
* TLSv1.3 (OUT), TLS handshake, Client hello (1):  
*  CAfile: /etc/ssl/certs/ca-certificates.crt  
*  CApath: none  
* OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to ident.me:443   
* Closing connection  
curl: (35) OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to ident.me:443   
[~]>$  
```  
and if you run tcpdump you will see the FIN+ACK like following   
![image](https://github.com/boostorg/beast/assets/19620677/e4ae01ae-bc01-4cb5-9f7a-6251d4bbb5ff)  
  
And if you comment all close actions on socket it will not make changes

---

## Comment 5

> Username: ashtum  
> Created at: 2023-08-30 10:37:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2731#issuecomment-1698915541  

I believe the problem is that you are writing `HTTP/1.1 200 Connection established\r\n` to socket while curl is expecting to do ssl handshake. indeed it is curl who closes the socket.  
have you tried to begin with https://github.com/boostorg/beast/tree/develop/example/http/server/async-ssl?

---

## Comment 6

> Username: MortezaBashsiz  
> Created at: 2023-08-30 10:47:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2731#issuecomment-1698928698  

@ashtum   
In http_proxy when the client wants to use https it tries to send at first the `CONNECT` request to the proxy server and then when client received `HTTP/1.1 200 Connection established\r\n` it knows that the connection is established and then it will start to do the ssl handshake with `client hello` as you see in dump result  
  
![image](https://github.com/boostorg/beast/assets/19620677/72ca89da-af17-48e7-b0d3-b744874074c0)  
  
  
Here you see the correct tcpdump and curl output example   
![image](https://github.com/boostorg/beast/assets/19620677/c38429ed-b0a4-4848-98c8-68f48d6c63a4)  
  
```  
  
[~]>$ curl https://ident.me -v  
* processing: https://ident.me  
* Uses proxy env variable https_proxy == '127.0.0.1:3128'  
*   Trying 127.0.0.1:3128...  
* Connected to 127.0.0.1 (127.0.0.1) port 3128  
* CONNECT tunnel: HTTP/1.1 negotiated  
* allocate connect buffer  
* Establish HTTP proxy tunnel to ident.me:443  
> CONNECT ident.me:443 HTTP/1.1  
> Host: ident.me:443  
> User-Agent: curl/8.2.1  
> Proxy-Connection: Keep-Alive  
>   
< HTTP/1.1 200 Connection established  
<   
* CONNECT phase completed  
* CONNECT tunnel established, response 200  
* ALPN: offers h2,http/1.1  
* TLSv1.3 (OUT), TLS handshake, Client hello (1):  
*  CAfile: /etc/ssl/certs/ca-certificates.crt  
*  CApath: none  
* TLSv1.3 (IN), TLS handshake, Server hello (2):  
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):  
* TLSv1.3 (IN), TLS handshake, Certificate (11):  
* TLSv1.3 (IN), TLS handshake, CERT verify (15):  
* TLSv1.3 (IN), TLS handshake, Finished (20):  
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):  
* TLSv1.3 (OUT), TLS handshake, Finished (20):  
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384  
* ALPN: server accepted h2  
* Server certificate:  
*  subject: CN=any.ident.me  
*  start date: Jul  5 23:23:29 2023 GMT  
*  expire date: Oct  3 23:23:28 2023 GMT  
*  subjectAltName: host "ident.me" matched cert's "ident.me"  
*  issuer: C=US; O=Let's Encrypt; CN=R3  
*  SSL certificate verify ok.  
* using HTTP/2  
* h2 [:method: GET]  
* h2 [:scheme: https]  
* h2 [:authority: ident.me]  
* h2 [:path: /]  
* h2 [user-agent: curl/8.2.1]  
* h2 [accept: */*]  
* Using Stream ID: 1  
> GET / HTTP/2  
> Host: ident.me  
> User-Agent: curl/8.2.1  
> Accept: */*  
>   
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):  
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):  
* old SSL session ID is stale, removing  
< HTTP/2 200   
< server: nginx  
< date: Wed, 30 Aug 2023 10:45:01 GMT  
< content-type: text/plain  
< content-length: 12  
< access-control-allow-origin: *  
< cache-control: no-cache, no-store, must-revalidate  
<   
* Connection #0 to host 127.0.0.1 left intact  
84.145.24.59[~]>$  
  
```

---

## Comment 7

> Username: ashtum  
> Created at: 2023-08-30 10:56:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2731#issuecomment-1698940123  

It seems you are shutting down the socket when write succeed:  
```C++  
	boost::asio::async_write(socket_, toBuffers("HTTP/1.1 200 Connection established\r\n"), [this, self](boost::system::error_code ec, std::size_t) {  
		if (!ec) {  
			boost::system::error_code ignored_ec;  
			socket_.shutdown(boost::asio::ip::tcp::socket::shutdown_both,  
				ignored_ec);  
		}  
		if (ec != boost::asio::error::operation_aborted) {  
			ConnectionManager_.stop(shared_from_this());  
		}  
	});  
```

---

## Comment 8

> Username: MortezaBashsiz  
> Created at: 2023-08-30 11:06:18 UTC  
> Updated at: 2023-08-30 11:08:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2731#issuecomment-1698952547  

@ashtum   
Thanks but I already mentioned that earlier in comments `And if you comment all close actions on socket it will not make changes`   
You can try to comment it out like   
```C++  
void Connection::doWrite() {  
	auto self(shared_from_this());  
	boost::asio::async_write(socket_, toBuffers("HTTP/1.1 200 Connection established\r\n"), [this, self](boost::system::error_code ec, std::size_t) {  
		if (!ec) {  
			// boost::system::error_code ignored_ec;  
			// socket_.shutdown(boost::asio::ip::tcp::socket::shutdown_both,  
			// 	ignored_ec);  
		}  
		if (ec != boost::asio::error::operation_aborted) {  
			ConnectionManager_.stop(shared_from_this());  
		}  
	});  
}  
  
```  
  
And see that it will not change the result

---

## Comment 9

> Username: ashtum  
> Created at: 2023-08-30 11:10:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2731#issuecomment-1698957666  

The second if still closes the connection, after commenting it curl prints:  
```BASH  
* error:0A00010B:SSL routines::wrong version number  
* Closing connection 0  
curl: (35) error:0A00010B:SSL routines::wrong version number  
```  
Which seems to be due to writing "HTTP/1.1 200 Connection established\r\n" for the second time.

---

## Comment 10

> Username: MortezaBashsiz  
> Created at: 2023-08-30 11:17:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2731#issuecomment-1698967117  

@ashtum   
Thank you so much for your help with the changes now I do not see the FIN+ACK anymore  
I will close the issue
