# #2016 - Getting end of stream error after second request of simple synchronous HTTP client [Closed]

> Username: GMellar  
> Created at: 2020-07-14 14:09:10 UTC  
> Updated at: 2020-07-16 13:30:28 UTC  
> Closed at: 2020-07-16 13:30:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2016  

I'm using boost beast from boost version 1.67. The example code below works for a single time when I keep the socket open. After the first request, the second one result in an error "end of stream". I keep the buffer forever inside the HTTPSession class. When I close the socket and connect again the request works again. Is there any way to keep that socket open with boost beast?  
  
```  
HTTPSession::HTTPSession(std::shared_ptr<boost::asio::io_context> io) : io(io), resolver(*io), socket(*io) {  
  
}  
  
HTTPSession::~HTTPSession() {  
}  
  
void HTTPSession::connect(const std::string &remote, unsigned int port) {  
	socket.close();  
	boost::asio::ip::tcp::resolver::query query(remote, "");  
	for (boost::asio::ip::tcp::resolver::iterator i = resolver.resolve(query);  
			i != boost::asio::ip::tcp::resolver::iterator(); ++i) {  
		this->ep = *i;  
		break;  
	}  
  
	this->port = port;  
	this->endpointAddress = remote;  
  
	this->ep.port(port);  
	socket.connect(this->ep);  
	boost::asio::socket_base::keep_alive option(true);  
	socket.set_option(option);  
}  
  
void HTTPSession::connect() {  
	socket.close();  
	socket.connect(this->ep);  
	boost::asio::socket_base::keep_alive option(true);  
	socket.set_option(option);  
}  
  
boost::beast::http::response<boost::beast::http::string_body> HTTPSession::syncRequest(  
		boost::beast::http::request<boost::beast::http::string_body> request) {  
	msg.reset(new Message);  
	msg->request = request;  
	boost::beast::http::write(socket, msg->request);  
	boost::beast::http::read(socket, this->buf, msg->response);  
	return std::move(msg->response);  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-07-14 14:10:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2016#issuecomment-658202988  

Show the code which builds the request. I think you are either using HTTP/1.0 or not using HTTP keep-alive.

---

## Comment 2

> Username: GMellar  
> Created at: 2020-07-14 15:05:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2016#issuecomment-658234323  

This is the code which builds the request:   
  
```  
std::vector<char> getBytes(std::uint16_t index,  
		std::uint16_t subindex) {  
	boost::beast::http::request<boost::beast::http::string_body> request;  
	request.version(11);  
	request.method(boost::beast::http::verb::get);  
	request.keep_alive(true);  
	request.set(boost::beast::http::field::connection, "keep-alive");  
	request.target((boost::format("/od/%04x/%02x") % index % subindex).str());  
	auto response = session.syncRequest(request);  
	return std::vector<char>(response.body().begin(), response.body().end());  
}  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-07-14 16:46:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2016#issuecomment-658289692  

And what is the response you are getting back?

---

## Comment 4

> Username: GMellar  
> Created at: 2020-07-14 16:59:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2016#issuecomment-658296363  

The response is a well formed HTTP 1.0 200 OK. Didn't see it before. Changing the protocol version to 1.0 does not resolve the problem.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-07-14 17:03:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2016#issuecomment-658298294  

Well, if the server refuses to keep the connection alive, there is nothing you can do. Beast is correctly reporting that the server is closing the connection. If you really want to fix this, you need to contact the server operator, and tell them to support keep-alive :)

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-07-16 13:30:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2016#issuecomment-659413090  

Seems to be resolved. Closing.
