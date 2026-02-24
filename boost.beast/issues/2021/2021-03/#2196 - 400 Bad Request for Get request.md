# #2196 - 400 Bad Request for Get request [Closed]

> Username: alezz94  
> Created at: 2021-03-16 21:15:37 UTC  
> Updated at: 2021-03-16 21:26:13 UTC  
> Closed at: 2021-03-16 21:26:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2196  

I am getting a 400 Bad request for a sync Http get query and I am not sure why.  
  
```  
// The io_context is required for all I/O  
    boost::asio::io_context ioc;  
  
    // These objects perform our I/O  
    tcp::resolver resolver{ioc};  
    tcp::socket socket{ioc};  
  
    // Look up the domain name  
    auto const results = resolver.resolve(m_httpHost, m_httpPort);  
    // Make the connection on the IP address we get from a lookup  
    boost::asio::connect(socket, results.begin(), results.end());  
    // Set up an HTTP GET request message  
    http::request<http::string_body> req{http::verb::get, "/api/v3/depth?symbol=BTCUSDT&limit=10", 11};  
    req.set(http::field::host, m_httpHost);  
    req.set(http::field::content_type, "application/json; charset=utf-8");  
    req.prepare_payload();  
    std::cout << req;  
    // Send the HTTP request to the remote host  
    http::write(socket, req);  
    // This buffer is used for reading and must be persisted  
    boost::beast::flat_buffer buffer;  
    // Declare a container to hold the response  
    http::response<http::dynamic_body> res;  
    // Receive the HTTP response  
    http::read(socket, buffer, res);  
    const std::string data = boost::beast::buffers_to_string(buffer.data());  
    std::cout << res << std::endl;  
```  
  
It manages to connect to the host and the it seems like the query is correct:  
(https://api.binance.com/api/v3/depth?symbol=BTCUSDT&limit=10 is correct), but I don't really understand why I am getting a 400 bad request failure.  
  
Could you please guys help me with this?  
  
Thanks.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-03-16 21:19:17 UTC  
> Updated at: 2021-03-16 21:19:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2196#issuecomment-800613240  

You aren't making the request using Secure HTTP, (i.e. using SSL). Show the entire actual response, please.

---

## Comment 2

> Username: alezz94  
> Created at: 2021-03-16 21:26:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2196#issuecomment-800617071  

That was indeed what was wrong with my request.  
Thanks a lot for the quick response.
