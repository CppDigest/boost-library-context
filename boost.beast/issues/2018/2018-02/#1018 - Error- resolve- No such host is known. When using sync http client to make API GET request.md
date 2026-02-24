# #1018 - Error: resolve: No such host is known. When using sync http client to make API GET request [Closed]

> Username: DragonOsman  
> Created at: 2018-02-12 22:43:04 UTC  
> Updated at: 2018-02-15 21:54:56 UTC  
> Closed at: 2018-02-15 21:54:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1018  

Here's my current code: https://gist.github.com/DragonOsman/9a7d4ee116efc575dece7d6de46bd31d  
  
This is the current output of the server app:  
```  
POST body example: -----------------------------7e23b7840184  
Content-Disposition: form-data; name="currency_amount"  
  
125.00  
-----------------------------7e23b7840184  
Content-Disposition: form-data; name="to_currency"  
  
PKR  
-----------------------------7e23b7840184--  
  
parsed_value map:  
currency_amount: 125.00  
to_currency: PKR  
Line 477: to_currency is: PKR  
Line 628: Error: resolve: No such host is known  
```  
  
Why is it saying that there's no such host?    The expected result should be the money amount converted to what it should be converted to.  Instead I get this exception.  Please help.  Thanks.

---

## Comment 1

> Username: DragonOsman  
> Created at: 2018-02-13 13:00:17 UTC  
> Updated at: 2018-02-13 13:02:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1018#issuecomment-365259792  

Okay, the problem with the host was resolved by putting only the string `openexchangerates.org` as the host and relying on the port being `443` for the `https` part.  Now there's another exception, though.  I caught it and the message came out to be: `end of stream`.  Thrown by `read`.

---

## Comment 2

> Username: DragonOsman  
> Created at: 2018-02-14 13:04:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1018#issuecomment-365600983  

According to the description of the `end_of_stream` error:  
```  
This error is returned under the following conditions:When attempting to read HTTP data from a stream and the stream read returns the error boost::asio::error::eof before any new octets have been received. When sending a complete HTTP message at once and the semantics of the message are that the connection should be closed to indicate the end of the message.   
```  
  
If I got the error because the complete message was received and the semantics of the message say that the connection be closed, how do I tell that that's what happened?  And if I want to close the connection, do I just do it after the call to `read` (although it seems that `read` itself is raising the exception)?

---

## Comment 3

> Username: DragonOsman  
> Created at: 2018-02-15 16:58:36 UTC  
> Updated at: 2018-02-15 17:00:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1018#issuecomment-365992056  

I changed the response-reading part of my code in the client part to this:  
```cpp  
                // This buffer is used for reading and must be persisted  
		boost::beast::flat_buffer buffer;  
  
		// Declare a container to hold the response  
		http::response<http::string_body> res;  
		res.keep_alive(true);  
		  
		try  
		{  
			// Receive the HTTP response  
			http::read(m_socket, buffer, res);  
		}  
		catch (const std::exception &e)  
		{  
			std::cerr << "Line 656: Error: " << e.what() << '\n';  
			if (buffer.size() < res.body().length())  
			{  
				std::unique_ptr<boost::beast::flat_buffer> buffer_ptr = std::make_unique<boost::beast::flat_buffer>(res.body().length() + 1);  
				buffer = boost::beast::flat_buffer{ *buffer_ptr };  
			}  
		}  
  
		// Gracefully close the socket  
		boost::beast::error_code ec;  
		m_socket.shutdown(tcp::socket::shutdown_both);  
```  
But it didn't work; I still get the `end_of_stream` error.    
  
How do I find out which of the two possible reasons caused it and then fix that?  Thanks in advance.

---

## Comment 4

> Username: DragonOsman  
> Created at: 2018-02-15 21:54:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1018#issuecomment-366074386  

Okay, since the problem I'd opened this issue for in the first place has already been solved, I'll close this and open a new one for the `end_of_stream` error and other problems I'm having right now.
