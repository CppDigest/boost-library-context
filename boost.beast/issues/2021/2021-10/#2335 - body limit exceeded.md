# #2335 - body limit exceeded [Closed]

> Username: Moerten  
> Created at: 2021-10-26 13:59:25 UTC  
> Updated at: 2025-12-11 17:23:01 UTC  
> Closed at: 2022-06-16 14:32:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2335  

Hi,  
  
I used a HTTP SSL socket to retrieve binary data (with a string_body). Now I want the receive a zip file and getting always a "body limit exceeded" error. I found out that I should set the parsers "body_limit" function to a bigger value somehow. But I am not using a "response_parser" so far. Maybe you could give an example how to change my code:  
  
Class-Members:  
```  
boost::beast::flat_buffer m_bufHttpSocket;  
boost::beast::http::response<boost::beast::http::string_body> m_oResponse;  
shared_ptr<boost::asio::ssl::context> m_pSSLContext;  
shared_ptr<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>> m_pHttpSocket;  
```  
Read-Funktion:  
```  
http::async_read(  
		*m_pHttpSocket,  
		m_bufHttpSocket,  
		m_oResponse,  
		std::bind(  
			&base::OnRead,  
			this,  
			std::placeholders::_1,  
			std::placeholders::_2));  
  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-10-26 14:28:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2335#issuecomment-951998796  

sure, let me knock up an example

---

## Comment 2

> Username: Moerten  
> Created at: 2021-11-05 14:34:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2335#issuecomment-961945232  

Do you have an example already?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-11-05 15:24:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2335#issuecomment-961988021  

> Do you have an example already?  
  
https://www.boost.org/doc/libs/1_77_0/libs/beast/doc/html/beast/using_http/parser_stream_operations/read_large_response_body.html

---

## Comment 4

> Username: Moerten  
> Created at: 2021-11-08 10:40:11 UTC  
> Updated at: 2021-11-08 10:44:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2335#issuecomment-963022132  

This example did not apply to my async http socket well. But I think I got the idea and managed to get it to work now (almost). I do the following:  
  
Class-Members:  
  
```  
boost::beast::flat_buffer m_bufHttpSocket;  
boost::beast::http::response<boost::beast::http::string_body> m_oResponse;  
shared_ptr<boost::beast::http::response_parser<boost::beast::http::string_body>> m_pParser;  
shared_ptr<boost::asio::ssl::context> m_pSSLContext;  
shared_ptr<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>> m_pHttpSocket;  
```  
  
Read-Funktion:  
```  
        if (m_pParser == nullptr)  
	{  
                m_pParser = make_shared<http::response_parser<http::string_body>>(std::move(m_oResponse));  
               	m_pParser->eager(true);  
	        m_pParser->body_limit(std::numeric_limits<std::uint64_t>::max());  
        }  
	http::async_read(  
		*m_pHttpSocket,  
		m_bufHttpSocket,  
		*m_pParser,  
		std::bind(  
			&base::OnRead,  
			this,  
			std::placeholders::_1,  
			std::placeholders::_2));  
```  
  
Read-Callback:  
```  
                if ((m_pParser != nullptr) && m_pParser->is_done())  
		{  
			m_oResponse = m_pParser->release();  
			m_pParser.reset();  
                         ....  
               }  
```  
  
I am still a bit confused about e.g. the parsers "content_length" / "content_length_remaining" properties. Also the parser states it "is_done" the "content_length_remaining" value says almost all bytes are missing? But this is obviously wrong.  
  
And the parsers needs a message object for it's constructor which gets moved to the parser. The "m_oResponse" message object is a member of my class. Now it gets moved to the parser. What when for some reasons never "m_oResponse = m_pParser->release()" gets called to move the message object back to my class? Then I guess I would have a broken "m_oResponse" member? How should this be handled?  
  
And more importingly. If I run tha app in release build now it works as expected. But not in debug build. Here I always get a "end of stream" error on first read. No idea why.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2335#issuecomment-1008220704  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: dvtate  
> Created at: 2025-12-11 17:23:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2335#issuecomment-3642966265  

Like Moerten, I was somewhat annoyed by the design of `http::parser`, but after doing something like Moerten's code but with `std::optional` instead of `std::shared_ptr`, I think I can live with it.  
  
I feel like in a perfect world a compiler would optimize  
```cpp  
m_req_parser.reset();  
m_req_parser.emplace(response<string_body>());  
m_req_parser->body_limit(boost::none);  
```  
into something comparable to a reusable parser that still handles the edge cases mentioned in #1681.
