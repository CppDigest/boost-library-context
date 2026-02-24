# #2422 - I am read only header - but got error about body [Closed]

> Username: fsmoke  
> Created at: 2022-05-06 14:58:05 UTC  
> Updated at: 2022-05-06 16:54:33 UTC  
> Closed at: 2022-05-06 16:54:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2422  

I've got ssl async client example to modify - and i want receive big file over https step by step incrementally, so i decided to use this example code   
  
https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/using_http/parser_stream_operations/incremental_read.html  
  
but in async way (read_header ->async_read_header and so on)  
  
from documentation  
> async_read_header  - Read **only** the header octets into a parser asynchronously from an AsyncWriteStream.  
  
but when i wrote simple code below - i've got a strange error - body limit exceeded...but why?? I only read header but not body  
   
```  
void on_write(beast::error_code ec, std::size_t bytes_transferred)  
		{  
			boost::ignore_unused(bytes_transferred);  
  
			if (ec)  
				return fail(ec, "write");  
  
			// Receive the HTTP response header  
			http::async_read_header(stream_, buffer_, parser_,  
				beast::bind_front_handler(  
					&session::on_read_header,  
					this->shared_from_this()));  
  
		}  
  
		void on_read_header(beast::error_code ec, std::size_t bytes_transferred)  
		{  
			boost::ignore_unused(bytes_transferred);  
  
			std::cout << "parser_.is_done(): " << parser_.is_done() << std::endl;  
			std::cout << "parser_.is_header_done(): " << parser_.is_header_done() << std::endl;  
  
			//if (ec)  
			//	return fail(ec, "read header"); // HERE ERROR body limit exceeded why ?? I read only header  
  
			auto & res = parser_.get();  
  
			auto len = parser_.content_length();  
			  
			if (len)  
				std::cout << "file size is:" << *len << std::endl;  
  
			// Set a timeout on the operation  
			beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));  
  
			// Gracefully close the stream  
			stream_.async_shutdown(  
				beast::bind_front_handler(  
					&session::on_shutdown,  
					this->shared_from_this()));  
		}  
```  
  
My compiler : Microsoft (R) C/C++ Optimizing Compiler Version 19.29.30140 for x86  
Version of BOOST is 1.79

---

## Comment 1

> Username: fsmoke  
> Created at: 2022-05-06 15:09:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2422#issuecomment-1119724028  

forgot parser is   
  
`http::parser<false, http::buffer_body> parser_;`

---

## Comment 2

> Username: sehe  
> Created at: 2022-05-06 15:30:30 UTC  
> Updated at: 2022-05-06 15:32:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2422#issuecomment-1119743145  

When the header is finished, the parser checks the content-length header against the body limit, if present. (reference: https://datatracker.ietf.org/doc/html/rfc7230#section-3.3)

---

## Comment 3

> Username: fsmoke  
> Created at: 2022-05-06 15:43:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2422#issuecomment-1119753815  

> checks the content-length header against the body limit  
  
ok, but which value of body limit i must to set to properly handle file of any size ? std::numeric_limits<uint64_t>::max() ?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-05-06 16:04:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2422#issuecomment-1119772298  

> std::numeric_limits<uint64_t>::max() ?  
  
That's a good choice

---

## Comment 5

> Username: fsmoke  
> Created at: 2022-05-06 16:54:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2422#issuecomment-1119812420  

Yeeep :)) Thanx very much - i've finally download 1gib file - successfully
