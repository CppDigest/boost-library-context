# #2849 - Websocket Async Client as a DLL [Closed]

> Username: Shreya-chikku  
> Created at: 2024-04-05 04:40:31 UTC  
> Updated at: 2024-05-06 08:01:46 UTC  
> Closed at: 2024-05-06 08:01:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2849  

A brief background on what I am trying to achieve:  
  
1. I have  
- An echo server (an async WebSocket server which returns back the same message received by the client)  
- A Client (C++ application let us name it Consumer.exe)  
- A dll - WebSocketClient.dll which is used by the Consumer.exe  
2. Consumer.exe will communicate with the server using WebSocket communication.  
3. This boost beast WebSocket async client is written in a WebSocketComm.dll  
4. During runtime Consumer.exe will load the dll using LoadLibrary() and perform the necessary read, write actions. (dll will be unloaded on application close)  
  
  
How should the dll work?  
  
1. The dll, once initiated, should continuously read the messages sent by the server.  
2. The dll should be able to send the messages sent by the Consumer.exe to the server at any given point in time. (Basically, continuous read-write operations should take place between the exe and server via dll at any given time)  
  
I have written the code for the same by taking the reference as [here ](https://www.boost.org/doc/libs/master/libs/beast/example/websocket/client/async/websocket_client_async.cpp) things are working fine.   
  
In a loop I am writing some data to the server by calling and immediately reading the response from the server using get_received_message().  
  
my expectation of code flow is:  
  
write(message) -> on_write() -> on_read() -> get_received_message()  
  
**_Issue 1:_**  
but this is not how it works; the flow is write(message)->on_write->get_received_message()->on_read()  
  
Due to this behavior, I am not able to read the response from the server immediately, only in the next read() operation I can read the response. But when I provide a sleep of 50ms between write() and get_received_message(), things work fine.  
  
**_Issue 2:_**  
I have observed that I need to call Sleep() function after calling the Connect() function and after the write() operation.  
  
Thanks in advance for any help. Sorry for any mistakes and a long code snippet.  
  
**Below is my code snippet for reference, please refer to the [CHECK] comment**  
  
WebSocketComm.cpp           
```  
void CWebSocketComm::run( char const* host, char const* port)  
 {  
	host_ = host;  
	port_ = port;  
  
	resolver_.async_resolve(  
		host_,  
		port_,  
		beast::bind_front_handler(  
			&CWebSocketComm::on_resolve,  
			shared_from_this()));  
  
  
	/*Do Not Miss This*/  
	ioc_.run();  
}  
```  
  
```  
void CWebSocketComm::on_handshake(beast::error_code ec)  
{  
	if (ec)  
		return fail(ec, "handshake");  
  
	//Initiate continous reading, so that we can catch any message sent by the server at any time  
	read();  
}  
```  
  
```  
void CWebSocketComm::write(std::string message)  
{  
	msgs_to_be_sent_.push(message);  
  
	if (!sending_)  
	{  
		sending_ = true;  
		// Send the message  
		ws_.async_write(  
			net::buffer(msgs_to_be_sent_.front()),  
			beast::bind_front_handler(  
				&CWebSocketComm::on_write,  
				shared_from_this()));  
	}  
}  
```  
  
```  
void CWebSocketComm::on_write(  
	beast::error_code ec,  
	std::size_t bytes_transferred)  
{  
	//Check if there are any pending messages to be sent  
       // If so, send those messages again using async_write()  
}  
```  
  
```  
void CWebSocketComm::read()  
{  
	// Read a message into our buffer  
	ws_.async_read(  
		buffer_,  
		beast::bind_front_handler(  
			&CWebSocketComm::on_read,  
			shared_from_this()));  
}  
```  
  
```  
void CWebSocketComm::on_read(  
	beast::error_code ec,  
	std::size_t bytes_transferred)  
{  
	boost::ignore_unused(bytes_transferred);  
  
	if (ec)  
		return fail(ec, "read");  
  
	/* save the latest 100 messages received from the server*/  
	std::string data = beast::buffers_to_string(buffer_.data());  
	if (msgs_received_from_server_.size() >= 100)  
	{  
		msgs_received_from_server_.pop();  
	}  
	msgs_received_from_server_.push(data);  
  
	/*clear the buffer and start reading again*/  
	buffer_.consume(buffer_.size());  
	read();  
}  
```  
  
```  
std::string CWebSocketComm::get_read_message()  
{  
	if (!msgs_received_from_server_.empty())  
	{  
		std::string data = msgs_received_from_server_.front();  
		msgs_received_from_server_.pop();  
		return data;  
	}  
  
	return std::string();  
}  
  
```  
  
Below are the exported functions used by the Consumer.exe to perform required actions.  
  
  
```  
std::shared_ptr<CWebSocketComm> CreateSocket()  
{  
	return std::make_shared<CWebSocketComm>();  
}  
  
void Connect(std::shared_ptr<CWebSocketComm> client, char const* host, char const* port)  
{  
	client->run(host, port);  
}  
  
void WriteData(std::shared_ptr<CWebSocketComm> client, std::string message)  
{  
	client->write(message);  
}  
  
std::string ReadData(std::shared_ptr<CWebSocketComm> client)  
{  
	return client->get_read_message();  
}  
  
void Disconnect(std::shared_ptr<CWebSocketComm> client)  
{  
	client->close();  
}  
```  
  
Consumer.cpp  
  
```  
// get the object  
// fnCreateSocket create = (fnCreateSocket)GetProcessAddress(hDll, "CreateSocket");  
CWebSocketComm client = create();  
  
//similarly get the address of other functions  
  
// Connect = connect_to_server  
connect_to_server(client, /*ip address*/,/*port*/);  
  
**//[CHECK]connect_to_server() needs to be added in a different thread,   
// ioc.run() from the dll will not return and hence the programs gets stuck and you cannot execute the below lines**  
  
**//[CHECK]//Sleep(100); // this is needed to ensure that all the initialization in the dll,  
//including read() present inside on_handshake() is started before we start writing to the server**  
  
// WriteData = write  
for (int i = 0; i < 5; ++i)  
{  
	write(client, std::to_string(i));  
}  
//Note that, just for reference it is written in a for loop, this could also be like on a button click some message is sent  
  
**//[CHECK]Sleep(100); // this is needed, otherwise things won't work**  
// ReadData = read  
std::string data;  
data = read(client);  
while (!data.empty())  
{  
        // do something with the data, may be display on the screen  
	data = read(client); // this will call get_read_message()  
}  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2024-04-05 11:12:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2849#issuecomment-2039533430  

The problem is that you are treating async operations as if they finish immediately. You need to have a mechanism to wait for operations until they complete.  
  
It is not clear how your code benefits from asynchronous operations, maybe you just need to use synchronous API. You can also use [asio::use_future](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/use_future.html) completion token to wait for operations. you can find an example here: https://github.com/boostorg/asio/blob/develop/example/cpp11/futures/daytime_client.cpp  
  
Generally speaking, asynchronous applications should be built around an executor (such as asio::any_io_executor) to efficiently and easily wait for asynchronous operations. Otherwise, scaling an application would become very difficult, requiring explicit synchronization mechanisms that make the code error-prone.
