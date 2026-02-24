# #2732 - How to read `client hello` with `boost::asio::ip::tcp::socket ` ? [Closed]

> Username: MortezaBashsiz  
> Created at: 2023-08-31 12:54:57 UTC  
> Updated at: 2023-09-04 13:40:49 UTC  
> Closed at: 2023-09-04 13:40:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2732  

I am trying to simulate tls handshake for a https request over https_proxy  
  
I have this code   
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
		} else if (ec != boost::asio::error::operation_aborted) {  
			ConnectionManager_.stop(shared_from_this());  
		}  
	});  
}  
  
void Connection::doWrite() {  
	auto self(shared_from_this());  
	boost::asio::async_write(socket_, toBuffers("HTTP/1.1 200 Connection established\r\n"), [this, self](boost::system::error_code ec, std::size_t) {  
	});  
	doRead();  
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
  
It will open port 8080 on 127.0.0.1 over tcp  
As you see I have printed the socket input as PACKET  
  
```C++  
std::cout << "PACKET : " << std::endl << data << std::endl;  
```  
And when I set https_proxy and do a curl, I am expecting to see all which comes to the socket as PACKET in output  
  
So the sequence is like this  
  
curl --- https://blah.com sends connect  request ---> main.cpp (127.0.0.1:8080)  
main.cpp --- return `HTTP/1.1 200 Connection established\r\n` ---> curl  
curl --- sends client hello ---> main.cpp  
  
But here I do not see the client hello request as PACKET and it is empty  
  
Build it  
```bash  
[~]>$ g++ main.cpp -o test  
```  
Run it  
```bash  
[~]>$ ./test  
```  
Set https_proxy and do curl  
```bash  
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
* OpenSSL/3.1.2: error:0A00010B:SSL routines::wrong version number  
* Closing connection  
curl: (35) OpenSSL/3.1.2: error:0A00010B:SSL routines::wrong version number  
```  
Check the output of main.cpp  
```bash  
[~/data/git/MortezaBashsiz/testboost]>$ ./test   
PACKET :   
CONNECT ident.me:443 HTTP/1.1  
Host: ident.me:443  
User-Agent: curl/8.2.1  
Proxy-Connection: Keep-Alive  
  
�hU  
PACKET :   
  
PACKET :   
  
```  
  
For proof that the client sends hello packet to main.cpp you can see in tcpdump output  
![image](https://github.com/boostorg/beast/assets/19620677/113a2285-16f0-40de-9c1a-72f6c0197579)  
  
As you see, there should be something in the second PACKET as output  
  
My question is where I made a mistake ?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-31 13:20:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2732#issuecomment-1701027825  

const_buffer captures by reference, i.e. the underlying memory needs to be kept alive until the operation completes. Your string (the first part of the request) goes out of memory once toBuffers returns.

---

## Comment 2

> Username: MortezaBashsiz  
> Created at: 2023-08-31 13:37:18 UTC  
> Updated at: 2023-08-31 13:42:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2732#issuecomment-1701056408  

@klemens-morgenstern   
Thank you so much for your answer  
TBH, I am not experienced in C++ and this is my first try, and I am trying to learn. I didn't understand what should I do to fix this code.  
  
On the other hand, I didn't get why the buffer is important   
main.cpp gets new request (client hello from curl)  
I expect to see it in output

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-08-31 13:47:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2732#issuecomment-1701073588  

You'll need to look into pointers and all the other stuff that make lose their mind in C++ i fear. try this first though:  
  
```cpp  
template<std::size_t N>  
std::array<boost::asio::const_buffer, 2u> toBuffers(const char (&str[N]))   
{  
       return {  
           boost::asio::buffer(str),   
           boost::asio::buffer("\r\n")  
       };  
};  
```

---

## Comment 4

> Username: MortezaBashsiz  
> Created at: 2023-08-31 17:35:44 UTC  
> Updated at: 2023-08-31 17:47:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2732#issuecomment-1701476731  

@klemens-morgenstern   
I got error while using this code  
```bash  
error: declaration of ‘str’ as array of references  
```  
  
BTW I am talking about this function  
  
```C++  
void Connection::doRead() {  
	auto self(shared_from_this());  
	socket_.async_read_some(boost::asio::buffer(buffer_), [this, self](boost::system::error_code ec, std::size_t bytesTransferred) {  
		char data[bytesTransferred];  
		std::memcpy(data, buffer_.data(), bytesTransferred);  
		std::cout << "PACKET : " << std::endl << data << std::endl;  
		if (!ec) {  
			doWrite();  
		} else if (ec != boost::asio::error::operation_aborted) {  
			ConnectionManager_.stop(shared_from_this());  
		}  
	});  
}  
```  
  
The line   
```C++  
std::cout << "PACKET : " << std::endl << data << std::endl;  
```  
Which is reading from socket with `socket_.async_read_some`   
  
My question is why I do not see `client hello` packet here ?

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2023-08-31 17:50:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2732#issuecomment-1701502111  

My bad:  
  
```cpp  
template<std::size_t N>  
std::array<boost::asio::const_buffer, 2u> toBuffers(const char (&str)[N])   
{  
       return {  
           boost::asio::buffer(str),   
           boost::asio::buffer("\r\n")  
       };  
};  
```

---

## Comment 6

> Username: MortezaBashsiz  
> Created at: 2023-09-01 05:50:32 UTC  
> Updated at: 2023-09-01 05:54:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2732#issuecomment-1702202323  

@klemens-morgenstern   
Thanks, code works, but it did not solve the issue  
I think here is a misunderstanding   
I tried to describe my issue in previous comment  
I don't think that function `toBuffers` is related to what I asked since it used in `doWrite` and I print the income packet in `doRead`  
I am talking about `client hello` request which is generated in client `curl` side and transferred to server `main.cpp`  
  
When I write randome stream on socket over tcp, it receives the packet and print it on output but for the request `client hello` from curl it does not print anything and the output is empty  
  
Writing to tcp socket   
```bash  
[~]>$ echo "aaaaaaaaaaaaa" > /dev/tcp/127.0.0.1/8080  
[~]>$  
```  
The output of main.cpp  
```bash  
PACKET :   
aaaaaaaaaaaaa  
```

---

## Comment 7

> Username: MortezaBashsiz  
> Created at: 2023-09-04 13:40:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2732#issuecomment-1705296840  

I had some tests around it  
I decided to implement it with pure socket then I wrote following code  
  
```c++  
#include <iostream>  
#include <string>  
#include <stdio.h>  
#include <sys/types.h>  
#include <sys/socket.h>  
#include <netinet/in.h>  
#include <arpa/inet.h>  
#include <stdlib.h>  
#include <unistd.h>  
#include <string.h>  
#include <netdb.h>  
#include <sys/uio.h>  
#include <sys/time.h>  
#include <sys/wait.h>  
#include <fcntl.h>  
#include <fstream>  
using namespace std;  
int main()  
{  
    char msg[1500];  
    sockaddr_in servAddr;  
    bzero((char*)&servAddr, sizeof(servAddr));  
    servAddr.sin_family = AF_INET;  
    servAddr.sin_addr.s_addr = htonl(INADDR_ANY);  
    servAddr.sin_port = htons(8080);  
    int serverSd = socket(AF_INET, SOCK_STREAM, 0);  
    int bindStatus = bind(serverSd, (struct sockaddr*) &servAddr,   
        sizeof(servAddr));  
    listen(serverSd, 5);  
    sockaddr_in newSockAddr;  
    socklen_t newSockAddrSize = sizeof(newSockAddr);  
    struct timeval start1, end1;  
    gettimeofday(&start1, NULL);  
    int bytesRead, bytesWritten = 0;  
    int newSd = accept(serverSd, (sockaddr *)&newSockAddr, &newSockAddrSize);  
    memset(&msg, 0, sizeof(msg));//clear the buffer  
    bytesRead += recv(newSd, (char*)&msg, sizeof(msg), 0);  
    cout << "PACKET : " << endl;  
    cout << msg << endl;   
    string response = "HTTP/1.1 200 Connection established\r\n\r\n";  
    strcpy(msg, response.c_str());  
    bytesWritten += send(newSd, (char*)&msg, strlen(msg), 0);  
    memset(&msg, 0, sizeof(msg));//clear the buffer  
    bytesRead += recv(newSd, (char*)&msg, sizeof(msg), 0);  
    cout << "PACKET : " << endl;  
    cout << msg << endl;   
    gettimeofday(&end1, NULL);  
    close(serverSd);  
    return 0;     
}  
```  
  
It will open port 8080  
And when you use this port as https_proxy   
```bash  
export https_proxy="127.0.0.1:8080"  
```  
And then use the curl to get some https url  
```bash  
curl -L https://google.com -v  
* processing: https://google.com  
* Uses proxy env variable https_proxy == '127.0.0.1:8080'  
*   Trying 127.0.0.1:8080...  
* Connected to 127.0.0.1 (127.0.0.1) port 8080  
* CONNECT tunnel: HTTP/1.1 negotiated  
* allocate connect buffer  
* Establish HTTP proxy tunnel to google.com:443  
> CONNECT google.com:443 HTTP/1.1  
> Host: google.com:443  
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
* OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to google.com:443   
* Closing connection  
curl: (35) OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to google.com:443   
  
```  
  
And the output of code is  
```bash  
./server  
PACKET :   
CONNECT google.com:443 HTTP/1.1  
Host: google.com:443  
User-Agent: curl/8.2.1  
Proxy-Connection: Keep-Alive  
  
  
PACKET :   
  
  
```  
  
As you see the second `PACKET:` is empty  
  
Which the request `client hello` has been sent from curl to port 8080  
  
![image](https://github.com/boostorg/beast/assets/19620677/5bad4163-06e6-4886-b50e-70aa4bbf74ab)  
  
  
I will close this ticket since I believe this is not boost problem and something is wrong from my side
