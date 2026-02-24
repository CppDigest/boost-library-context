# #1040 - Enhansement request to http_server_async.cpp [Closed]

> Username: carolinebeltran  
> Created at: 2018-02-25 19:27:06 UTC  
> Updated at: 2018-02-27 03:48:40 UTC  
> Closed at: 2018-02-27 03:48:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1040  

This demo demonstrates how to serve files but I would like the modification to handle my own dynamically generated result.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-26 01:40:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1040#issuecomment-368366773  

Sorry, I pressed Edit instead of Comment. If you could please Open a new issue and repeat your question I will answer it correctly

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-02-26 01:40:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1040#issuecomment-368366788  

>This demo demonstrates how to serve files but I would like the modification to handle my own dynamically generated result.  
  
The servers already do that by returning error responses, for example:  
https://github.com/boostorg/beast/blob/fc187257c300301e616c61d11baaee79072eef6e/example/advanced/server/advanced_server.cpp#L117  
  
To return your own generated content just declare the response to use the `http::string_body` type, fill in the object including the body, call `prepare_payload`, and then write it. Should be very simple.

---

## Comment 3

> Username: carolinebeltran  
> Created at: 2018-02-26 17:19:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1040#issuecomment-368578120  

Thank you for responding Vinnie.  I actually took an approach similar to **http_server_small.cpp** demo; it appears to obtain posted form data correctly:  
  
`	if (req.method() == http::verb::post)  
	{  
		std::cout << req.body() << std::endl;  // firstname=Mickey&lastname=Mouse  
  
		http::response<http::dynamic_body> res;  
		res.result(http::status::ok);  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		boost::beast::ostream(res.body())  
			<< "<html>\n"  
			<< "<head><title>HTTP POST response</title></head>\n"  
			<< "<body>\n"  
			<< "<h1>hello world</h1>\n"  
			<< "</body>\n"  
			<< "</html>\n";  
  
		res.content_length(res.body().size());  
  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
`  
Then below I use your **http_server_async.cpp** code to handle GET/HEAD methods.  Does this look correct to you?  And secondly, I am assuming that we are completely responsible for handling all form, URL and cookie parsing.  Is this correct?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-02-26 17:40:19 UTC  
> Updated at: 2018-02-26 18:06:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1040#issuecomment-368584850  

>Does this look correct to you?  
  
Yes, that is correct. But you could also write:  
```  
http::response<http::string_body> res;  
...  
res.body() =  
    "<html>\n"  
    "<head><title>HTTP POST response</title></head>\n"  
    "<body>\n"  
    "<h1>hello world</h1>\n"  
    "</body>\n"  
    "</html>\n";  
```  
  
which is a tiny bit more efficient since it only appends one string instead of several. Either way works.  
  
>I am assuming that we are completely responsible for handling all form, URL and cookie parsing. Is this correct?  
  
Yes that is correct. I'm hoping someone will make an open source library that uses Beast's message model that does all of these things (hint hint!)

---

## Comment 5

> Username: carolinebeltran  
> Created at: 2018-02-27 03:48:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1040#issuecomment-368738369  

Thank you for your comments, I have a pretty good understanding how I can handle dynamic content now.
