# #958 - Adding POST handling code to http_server_async.cpp Example File [Closed]

> Username: DragonOsman  
> Created at: 2018-01-01 14:39:53 UTC  
> Updated at: 2019-01-14 21:51:59 UTC  
> Closed at: 2018-01-18 22:45:59 UTC  
> Url: https://github.com/boostorg/beast/issues/958  

I want to create a web app that implements a currency converter with a Google Map GUI.  An info window centered on the user's geolocation coordinates obtained by using HTML5 geolocation will pop up when the application starts.  By default, the "from" currency would be the currency used at the country or whatever place the user is in, but it can be changed with the drop-down menu on the HTML form displayed on the info window.  The form would have an input field where the user would enter a money amount, and two drop-down menus: one for the "from" currency and the other for the "to" currency.  The form would send the inputted data to the server code via POST.  This is what I want to implement.  
  
What I'm trying to do for that is to take the http_server_async.cpp example code and add code to it to handle the POST method.  The request-handling function from the example is like this now:   
```cpp  
// This function produces an HTTP response for the given  
// request. The type of the response object depends on the  
// contents of the request, so the interface requires the  
// caller to pass a generic lambda for receiving the response.  
template<  
	class Body, class Allocator,  
	class Send>  
	void  
	handle_request(  
		boost::beast::string_view doc_root,  
		http::request<Body, http::basic_fields<Allocator>>&& req,  
		Send&& send)  
{  
	// Returns a bad request response  
	auto const bad_request =  
		[&req](boost::beast::string_view why)  
	{  
		http::response<http::string_body> res{ http::status::bad_request, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = why.to_string();  
		res.prepare_payload();  
		return res;  
	};  
  
	// Returns a not found response  
	auto const not_found =  
		[&req](boost::beast::string_view target)  
	{  
		http::response<http::string_body> res{ http::status::not_found, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "The resource '" + target.to_string() + "' was not found.";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Returns a server error response  
	auto const server_error =  
		[&req](boost::beast::string_view what)  
	{  
		http::response<http::string_body> res{ http::status::internal_server_error, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "An error occurred: '" + what.to_string() + "'";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Make sure we can handle the method  
	if (req.method() != http::verb::get &&  
		req.method() != http::verb::head &&  
		req.method() != http::verb::post)  
		return send(bad_request("Unknown HTTP-method"));  
  
	// Request path must be absolute and not contain "..".  
	if (req.target().empty() ||  
		req.target()[0] != '/' ||  
		req.target().find("..") != boost::beast::string_view::npos)  
		return send(bad_request("Illegal request-target"));  
  
	// Build the path to the requested file  
	std::string path = path_cat(doc_root, req.target());  
	if (req.target().back() == '/')  
		path.append("index.html");  
  
	// Attempt to open the file  
	boost::beast::error_code ec;  
	http::file_body::value_type body;  
	body.open(path.c_str(), boost::beast::file_mode::scan, ec);  
  
	// Handle the case where the file doesn't exist  
	if (ec == boost::system::errc::no_such_file_or_directory)  
		return send(not_found(req.target()));  
  
	// Handle an unknown error  
	if (ec)  
		return send(server_error(ec.message()));  
  
	// Respond to HEAD request  
	if (req.method() == http::verb::head)  
	{  
		http::response<http::empty_body> res{ http::status::ok, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to GET request  
	else if (req.method() == http::verb::get)  
	{  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to POST request  
	else if (req.method() == http::verb::post)  
	{  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
	}  
	return send(std::move(res));  
}  
```  
  
I need to parse the URI and check if the path is what I expect, right?  And then I have to decode the request to check the encoding and decode it.  I have to check the Content-type for the encoding, right?  If so, how should I do that?    
  
So for the path, what I'm thinking of doing is checking the req.target() object and to see if there's anything before the '/' character other than a space, and if the path itself is what I expect.  I need a way to find the dot character and see what the extension is as well.  Do I need a regex library for URIs for this?  A URI regex library and a mimetic library.  Or is there also an easy way to do this without those libraries that I should know?  Like perhaps using a string_view to check the request target and encoding.  Would that be possible?  I should say I'm still learning and that this is the first time I'm trying to make this kind of library (and I'm also new to network programming itself).  But I want to see how to do this myself as well, if just because I don't want to have to deal with too many libraries at once.    
  
I'm only working on the server code for now and then I'll do the client; I haven't done anything for the application code itself because I wanted to get the server and client code done first.  But what I'm thinking of is having a CGI script receive the POST request from the form.  I've configured my Apache server to accept .cgi and .exe extensions for that, by the way.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-01 15:48:53 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-354659962  

Great, thanks for opening this as an issue!  
  
>I need to parse the URI and check if the path is what I expect, right?  
  
Yes, you need to check the `req.target()` string. Beast doesn't parse the URI it just does light validation (make sure there are no illegal characters or disallowed white space) before presenting the string.  
  
>I have to decode the request to check the encoding and decode it  
  
Beast will automatically handle the case where "chunked" is specified as the last Transfer-Encoding, but everything else is up to you. Specifically, if the Content-Type field specifies "application/x-www-form-urlencoded" or "multipart/form-data" (two encodings often used for POST message payloads) then you will have to decode that yourself.  
  
The reason is because Beast is a low-level library. It just takes care of modeling the message in the most basic form, and sending and receiving those messages. My hope is that someone will write a library that builds on Beast and takes care of things like this. There is already a library out there which I believe handles those encodings but it was not written specifically for Beast. This may or may not be helpful:  
  
http://www.codesink.org/mimetic_mime_library.html  
  
>I have to check the Content-type for the encoding, right? If so, how should I do that?  
  
The expression `req[http::field::content_type]` will return a `string_view` that you can inspect. You will have to parse this value according to the rules for HTTP *media-type*:  
  
https://en.wikipedia.org/wiki/Media_type  
  
>checking the req.target() object and to see if there's anything before the '/' character other than a space  
  
You should never see spaces in the target, that would be an invalid HTTP message  
  
> I need a way to find the dot character and see what the extension is as well. Do I need a regex library for URIs for this? A URI regex library and a mimetic library. Or is there also an easy way to do this without those libraries that I should know? Like perhaps using a string_view to check the request target and encoding. Would that be possible?  
  
There are many ways to do this, the regex is probably the quickest.  
  
Hope this helps!

---

## Comment 2

> Username: DragonOsman  
> Created at: 2018-01-01 22:18:34 UTC  
> Updated at: 2018-01-01 22:36:55 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-354681151  

To check the `req.target()` to see if it begins with a `\` character, I should do `req.target().front()`, right?  Since checking for the root directory (`\`) involves `.back()` (I assume it's because it's just the one character in the string).  And then I need to use a mimetic library and a regex library to locate the '.' character and check the extension?    
  
For now, I did it like this (for the path string):  
```cpp  
else if (req.target().front() == '/' && req.target().size() > 1)  
{  
	std::string str{ req.target };  
	std::stringstream ss{ req.target() };  
	ss >> str;  
	using boost::beast::iequals;  
	auto const ext = [&path]  
	{  
		auto const pos = path.rfind(".");  
		if (pos == boost::beast::string_view::npos)  
		{  
                     return boost::beast::string_view{};  
                }  
		return path.substr(pos);  
	}();  
	if (!iequals(ext, ".cgi") && !iequals(ext, ".exe"))  
	{  
		return send(bad_request("Bad request"));  
	}  
}  
```  
  
And this is what I for the POST method if-condition itself:  
```cpp  
// Respond to POST request  
else if (req.method() == http::verb::post)  
{  
	boost::beast::string_view content_type = req[http::field::content_type];  
	http::response<http::file_body> res{  
		std::piecewise_construct,  
		std::make_tuple(std::move(body)),  
		std::make_tuple(http::status::ok, req.version()) };  
	res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
	res.set(http::field::content_type, mime_type(path));  
	res.content_length(body.size());  
	res.keep_alive(req.keep_alive());  
}  
return send(std::move(res));  
```  
  
I need to find out how to parse the Content-type now.  Aside from that, is it alright so far?  
  
When I said I'd check for a space, I meant a space between the '/' character marking the start of the path string and after method verb.  There has to be a space there, right?  "method SP path SP version".

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-01-01 22:27:55 UTC  
> Updated at: 2018-01-01 22:28:19 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-354681545  

>When I said I'd check for a space, I meant a space between the '/' character marking the start of the path string and after method verb. There has to be a space there, right? "method SP path SP version".  
  
You won't see those spaces, Beast message observers return just _method_, _path_, and _version_ (as an integer, either 10 for 1.0 or 11 for 1.1).  
  
>is it alright so far?  
  
Yes you're on the right track

---

## Comment 4

> Username: DragonOsman  
> Created at: 2018-01-01 22:33:58 UTC  
> Updated at: 2018-01-02 01:04:01 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-354681790  

Oh, okay, got it.  I'll try to see how to parse and decode the Content-type field now, then.    
  
Edit: I made a small typo in the other post.  I meant to say I'd check for the '/' character at the beginning of the path.  I'll edit it.  
  
Edit2: Okay, so about the `string_view` returned from `req[http::field::content_type]`.  Is it going to have the Content-type value in it, or do I have to get the value?  
  
Edit3: I've done it up to the Content-Type:  
```cpp  
// This function produces an HTTP response for the given  
// request. The type of the response object depends on the  
// contents of the request, so the interface requires the  
// caller to pass a generic lambda for receiving the response.  
template<  
	class Body, class Allocator,  
	class Send>  
	void  
	handle_request(  
		boost::beast::string_view doc_root,  
		http::request<Body, http::basic_fields<Allocator>>&& req,  
		Send&& send)  
{  
	// Returns a bad request response  
	auto const bad_request =  
		[&req](boost::beast::string_view why)  
	{  
		http::response<http::string_body> res{ http::status::bad_request, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = why.to_string();  
		res.prepare_payload();  
		return res;  
	};  
  
	// Returns a not found response  
	auto const not_found =  
		[&req](boost::beast::string_view target)  
	{  
		http::response<http::string_body> res{ http::status::not_found, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "The resource '" + target.to_string() + "' was not found.";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Returns a server error response  
	auto const server_error =  
		[&req](boost::beast::string_view what)  
	{  
		http::response<http::string_body> res{ http::status::internal_server_error, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "An error occurred: '" + what.to_string() + "'";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Make sure we can handle the method  
	if (req.method() != http::verb::get &&  
		req.method() != http::verb::head &&  
		req.method() != http::verb::post)  
	{  
		return send(bad_request("Unknown HTTP-method"));  
	}  
  
	// Request path must be absolute and not contain "..".  
	if (req.target().empty() ||  
		req.target()[0] != '/' ||  
		req.target().find("..") != boost::beast::string_view::npos)  
	{  
		return send(bad_request("Illegal request-target"));  
	}  
  
	// Build the path to the requested file  
	std::string path = path_cat(doc_root, req.target());  
	if (req.target().back() == '/')  
	{  
		path.append("index.html");  
	}  
  
	// In case of POST request, check the path URI  
	else if (req.target().front() == '/' && req.target().size() > 1)  
	{  
		std::string str{ req.target };  
		std::stringstream ss{ req.target() };  
		ss >> str;  
		using boost::beast::iequals;  
		const auto ext = [&path]  
		{  
			auto const pos = path.rfind(".");  
			if (pos == boost::beast::string_view::npos)  
			{  
				return send(bad_request("Illegal request-target"));			  
                        }	  
                }();  
		if (!iequals(ext, ".cgi") && !iequals(ext, ".exe"))  
		{  
			return send(bad_request("Illegal request-target"));  
		}  
	}  
  
	// Attempt to open the file  
	boost::beast::error_code ec;  
	http::file_body::value_type body;  
	body.open(path.c_str(), boost::beast::file_mode::scan, ec);  
  
	// Handle the case where the file doesn't exist  
	if (ec == boost::system::errc::no_such_file_or_directory)  
	{  
		return send(not_found(req.target()));  
	}  
  
	// Handle an unknown error  
	if (ec)  
	{  
		return send(server_error(ec.message()));  
	}  
  
	// Respond to HEAD request  
	if (req.method() == http::verb::head)  
	{  
		http::response<http::empty_body> res{ http::status::ok, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to GET request  
	else if (req.method() == http::verb::get)  
	{  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to POST request  
	else if (req.method() == http::verb::post)  
	{  
		boost::beast::string_view content_type = req[http::field::content_type];  
		if (content_type != "multipart/form-data" && content_type != "application/x-www-form-urlencoded")  
		{  
			return send(bad_request("Bad request"));  
		}  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
	}  
	return send(std::move(res));  
}  
```  
Is that okay so far?  If so, where should I put the code for constructing the response to send to the client?  Or should I do that in the CGI script itself since the form would submit to that script?    
  
Edit: Just fixed return statement problem in path parsing code for POST method. (Oops)

---

## Comment 5

> Username: DragonOsman  
> Created at: 2018-01-02 09:48:33 UTC  
> Updated at: 2018-01-02 09:51:17 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-354731126  

I just took out the use of `std::stringstream` since I wasn't actually using the variable anywhere.  Current code:  
```cpp  
// This function produces an HTTP response for the given  
// request. The type of the response object depends on the  
// contents of the request, so the interface requires the  
// caller to pass a generic lambda for receiving the response.  
template<  
	class Body, class Allocator,  
	class Send>  
	void  
	handle_request(  
		boost::beast::string_view doc_root,  
		http::request<Body, http::basic_fields<Allocator>>&& req,  
		Send&& send)  
{  
	// Returns a bad request response  
	auto const bad_request =  
		[&req](boost::beast::string_view why)  
	{  
		http::response<http::string_body> res{ http::status::bad_request, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = why.to_string();  
		res.prepare_payload();  
		return res;  
	};  
  
	// Returns a not found response  
	auto const not_found =  
		[&req](boost::beast::string_view target)  
	{  
		http::response<http::string_body> res{ http::status::not_found, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "The resource '" + target.to_string() + "' was not found.";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Returns a server error response  
	auto const server_error =  
		[&req](boost::beast::string_view what)  
	{  
		http::response<http::string_body> res{ http::status::internal_server_error, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "An error occurred: '" + what.to_string() + "'";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Make sure we can handle the method  
	if (req.method() != http::verb::get &&  
		req.method() != http::verb::head &&  
		req.method() != http::verb::post)  
	{  
		return send(bad_request("Unknown HTTP-method"));  
	}  
  
	// Request path must be absolute and not contain "..".  
	if (req.target().empty() ||  
		req.target()[0] != '/' ||  
		req.target().find("..") != boost::beast::string_view::npos)  
	{  
		return send(bad_request("Illegal request-target"));  
	}  
  
	// Build the path to the requested file  
	std::string path = path_cat(doc_root, req.target());  
	if (req.target().back() == '/')  
	{  
		path.append("index.html");  
	}  
  
	// In case of POST request, check the path URI  
	else if (req.target().front() == '/' && req.target().size() > 1)  
	{  
		using boost::beast::iequals;  
		const auto ext = [&path]  
		{  
			const auto pos = path.rfind(".");  
			if (pos == boost::beast::string_view::npos)  
			{  
				return send(bad_request("Illegal request-target"));  
			}  
			const auto pos2 = path.find(" ");  
			if (pos != boost::beast::string_view::npos)  
			{  
				return send(bad_request("Illegal request-target"));  
			}  
		}();  
		if (!iequals(ext, ".cgi") && !iequals(ext, ".exe"))  
		{  
			return send(bad_request("Illegal request-target"));  
		}  
	}  
  
	// Attempt to open the file  
	boost::beast::error_code ec;  
	http::file_body::value_type body;  
	body.open(path.c_str(), boost::beast::file_mode::scan, ec);  
  
	// Handle the case where the file doesn't exist  
	if (ec == boost::system::errc::no_such_file_or_directory)  
	{  
		return send(not_found(req.target()));  
	}  
  
	// Handle an unknown error  
	if (ec)  
	{  
		return send(server_error(ec.message()));  
	}  
  
	// Respond to HEAD request  
	if (req.method() == http::verb::head)  
	{  
		http::response<http::empty_body> res{ http::status::ok, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to GET request  
	else if (req.method() == http::verb::get)  
	{  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to POST request  
	else if (req.method() == http::verb::post)  
	{  
		boost::beast::string_view content_type = req[http::field::content_type];  
		if (content_type != "multipart/form-data" && content_type != "application/x-www-form-urlencoded")  
		{  
			return send(bad_request("Bad request"));  
		}  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
	}  
	return send(std::move(res));  
}  
```  
I haven't touched the rest of the file yet.  Do I need to?  I do plan on making it a header file and taking out main.  But I'll do that later.   But aside from that, is it okay so far?  
  
Also, would it be fine to use `std::string_view` in place of the `boost::beast` one if C++17 support is enabled?  Same for the standard library error codes (since that's already there since before C++17).

---

## Comment 6

> Username: djarek  
> Created at: 2018-01-02 09:56:22 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-354732317  

@DragonOsman   
Unfortunately Boost.ASIO does not support std::error_code, because it has a hard dependency on Boost.System. AFAIK, boost's error_code is convertible to std::error_code.  
Since Beast is dependent on ASIO, there's not much that can be done without a lot of code being broken.  
  
std::string_view could be usable in Beast, but it's currently not used AFAIK.

---

## Comment 7

> Username: DragonOsman  
> Created at: 2018-01-02 09:58:26 UTC  
> Updated at: 2018-01-02 12:37:22 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-354732814  

So should I use `std::string_view`, then?  Or is it not advised?  And how can I have boost's error_code convert to the standard library one?  
  
Edit: And what else do I still need to do here?  Is the POST-handling code complete?  
  
Edit2: I also want to ask how to set the response message.  I'm looking at the message class constructors, and I found some for when isRequest is false that take either an argument to the body or an argument to the body and argument to the header fields.  I'm confused as to whether I should use that to set the response body string.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-01-02 14:54:30 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-354783039  

>So should I use std::string_view, then?  
  
That won't work, all of Beast's interfaces use `boost::string_view`.  
  
>...how can I have boost's error_code convert to the standard library one?  
  
Asio uses `boost::system::error_code`, so you will also have to use that. The type alias `boost::beast::error_code` is provided for this purpose.  
  
>I also want to ask how to set the response message  
  
You can set the body in the constructor as shown in the code you provided:  
  
```  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
```  
  
Or you can assign it using the accessor, also shown in the code you provided:  
  
```  
		res.body() = "The resource '" + target.to_string() + "' was not found.";  
```  
  
Note that the example code uses two different body types: `string_body` and `file_body`. For `string_body`, the body uses a `std::string`. For `file_body` it uses its own custom type which holds a file handle.

---

## Comment 9

> Username: DragonOsman  
> Created at: 2018-01-02 21:36:49 UTC  
> Updated at: 2018-01-02 22:46:21 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-354881579  

What changes should I make to the current http_client_async.cpp file to make it work with the server code I just wrote?  And what do I do with the main functions from both files?  Also, I'm having trouble creating a header plus cpp file pair out of the client code file.  I left the server file as a header because it has templates.    
  
Edit: Just saw your post.  Sorry about that.  
  
So is my POST-handling code fine now?  If so, I'll move on to the client for now.  But I do need to know what to do the current http_client_async.cpp file like I said.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-01-02 22:41:03 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-354894700  

I'm not sure how to answer that... are you asking how to send a POST request? You need a `main` in any program that you want to link. Why are you having a problem splitting the client code?

---

## Comment 11

> Username: DragonOsman  
> Created at: 2018-01-03 14:25:44 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355023991  

A header file and its accompanying source code file shouldn't have `main` in it, so that's why I was trying to ask what to do with the code in `main` in the server and client code because of that.    
  
When I was trying to split the client code, I had an error about the `session::run()` function not being the same when I try to put its definition in a separate source code file.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2018-01-03 14:34:46 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355026191  

I'd have to see the files to speak confidently about it.

---

## Comment 13

> Username: DragonOsman  
> Created at: 2018-01-03 20:36:12 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355120037  

I'll post the code for my client here, then:  
```cpp  
#ifndef HTTP_CLIENT_HPP_  
#define HTTP_CLIENT_HPP_  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
namespace http = boost::beast::http;    // from <boost/beast/http.hpp>  
  
										//------------------------------------------------------------------------------  
  
										// Report a failure  
void  
fail(boost::system::error_code ec, char const* what);  
  
// Performs an HTTP GET and prints the response  
class session : public std::enable_shared_from_this<session>  
{  
	tcp::resolver resolver_;  
	tcp::socket socket_;  
	boost::beast::flat_buffer buffer_; // (Must persist between reads)  
	http::request<http::empty_body> req_;  
	http::response<http::string_body> res_;  
  
public:  
	// Resolver and socket require an io_context  
	explicit  
		session(boost::asio::io_context& ioc)  
		: resolver_(ioc)  
		, socket_(ioc)  
	{  
	}  
  
	// Start the asynchronous operation  
	void  
		run(  
			char const* host,  
			char const* port,  
			char const* target,  
			int version,  
			boost::beast::http::verb verb)  
	{  
		// Set up an HTTP request message  
		req_.version(version);  
		req_.method(verb);  
		req_.target(target);  
		req_.set(http::field::host, host);  
		req_.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
		// Look up the domain name  
		resolver_.async_resolve(  
			host,  
			port,  
			std::bind(  
				&session::on_resolve,  
				shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2));  
	}  
  
	void  
		on_resolve(  
			boost::system::error_code ec,  
			tcp::resolver::results_type results)  
	{  
		if (ec)  
			return fail(ec, "resolve");  
  
		// Make the connection on the IP address we get from a lookup  
		boost::asio::async_connect(  
			socket_,  
			results.begin(),  
			results.end(),  
			std::bind(  
				&session::on_connect,  
				shared_from_this(),  
				std::placeholders::_1));  
	}  
  
	void  
		on_connect(boost::system::error_code ec)  
	{  
		if (ec)  
			return fail(ec, "connect");  
  
		// Send the HTTP request to the remote host  
		http::async_write(socket_, req_,  
			std::bind(  
				&session::on_write,  
				shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2));  
	}  
  
	void  
		on_write(  
			boost::system::error_code ec,  
			std::size_t bytes_transferred)  
	{  
		boost::ignore_unused(bytes_transferred);  
  
		if (ec)  
			return fail(ec, "write");  
  
		// Receive the HTTP response  
		http::async_read(socket_, buffer_, res_,  
			std::bind(  
				&session::on_read,  
				shared_from_this(),  
				std::placeholders::_1,  
				std::placeholders::_2));  
	}  
  
	void  
		on_read(  
			boost::system::error_code ec,  
			std::size_t bytes_transferred)  
	{  
		boost::ignore_unused(bytes_transferred);  
  
		if (ec)  
			return fail(ec, "read");  
  
		// Write the message to standard out  
		std::cout << res_ << std::endl;  
  
		// Gracefully close the socket  
		socket_.shutdown(tcp::socket::shutdown_both, ec);  
  
		// not_connected happens sometimes so don't bother reporting it.  
		if (ec && ec != boost::system::errc::not_connected)  
			return fail(ec, "shutdown");  
  
		// If we get here then the connection is closed gracefully  
	}  
};  
  
#endif  
```  
The server code file is basically the same as in the original example, with the code I showed here added to it for the POST verb handling.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2018-01-03 21:14:26 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355129019  

And this is why we use spaces over tabs :)  
  
Any reason why you are using the asynchronous client instead of the synchronous one?

---

## Comment 15

> Username: DragonOsman  
> Created at: 2018-01-04 12:28:13 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355270366  

Because I'm using the asynchronous server.  And the reason for that is because I don't want to make blocking requests and responses.  Is that not a good idea?  
  
I want to ask: where and how do I put the code for making the requests using methods other than GET?

---

## Comment 16

> Username: vinniefalco  
> Created at: 2018-01-04 14:43:32 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355299395  

Just because the server is asynchronous does not mean the client has to also use the asynchronous interfaces. To do a POST just replace the GET request with the POST request in the client and you should be set.

---

## Comment 17

> Username: DragonOsman  
> Created at: 2018-01-04 16:59:32 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355337617  

I have to allow the client to have an interface where it can make a request either of the three methods that the server supports, right?  But what else do I need to do aside from storing the method value in a variable?  
  
As for the asynchronous vs. synchronous thing.  I guess I'll use the synchronous one, then.

---

## Comment 18

> Username: vinniefalco  
> Created at: 2018-01-04 17:49:57 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355350814  

>But what else do I need to do aside from storing the method value in a variable?  
  
If you use POST then you need a message body...it really is up to you to structure the client any way that you want. There are no "rules" other than, you fill out the message and send it.

---

## Comment 19

> Username: DragonOsman  
> Created at: 2018-01-04 18:24:51 UTC  
> Updated at: 2018-01-04 18:37:01 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355359629  

Okay, here's the client code I have now:  
```cpp  
#ifndef HTTP_CLIENT_HPP_  
#define HTTP_CLIENT_HPP_  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <stdexcept>  
#include <string>  
  
namespace http = boost::beast::http;    // from <boost/beast/http.hpp>  
  
using namespace http;  
  
class HttpClient  
{  
public:  
	  
	HttpClient(char *host, char *port, char *target, const int version, const verb &verb)  
		: m_host{ host }, m_port{ port }, m_target{ target }, m_version{ version }, m_verb{ verb }  
	{  
		if (verb != verb::get || verb != verb::post || verb != verb::head)  
		{  
			throw std::runtime_error{ "unknown HTTP method" };  
		}  
		if (version != 10 || version != 11)  
		{  
			throw std::runtime_error{ "unsupported HTTP version" };  
		}  
	}  
	char *get_host() const { return m_host; }  
	char *get_port() const { return m_port; }  
	char *get_target() const { return m_target; }  
	verb get_verb() const { return m_verb; }  
	int get_version() const { return m_version; }  
	void set_host(char *host) { m_host = host; }  
	void set_port(char *port) { m_port = port; }  
	void set_target(char *target) { m_target = target; }  
	void set_verb(const verb &verb) { m_verb = verb; }  
  
private:  
	char *m_host;  
	char *m_port;  
	char *m_target;  
	verb m_verb;  
	int m_version;  
};  
  
#endif  
```  
And this is the server code:  
```cpp  
#ifndef HTTP_SERVER_HPP_  
#define HTTP_SERVER_HPP_  
  
// Osman Zakir  
// 1 / 2 / 2018  
// Asynchronous HTTP server using Boost.Beast  
// This server implementation uses the example http_server_async.cpp from   
// Beast examples  
// Most of the credit goes to Vinnie Falco, who wrote the original example code  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/bind_executor.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/config.hpp>  
#include <algorithm>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
#include <vector>  
  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
namespace http = boost::beast::http;    // from <boost/beast/http.hpp>  
  
										// Return a reasonable mime type based on the extension of a file.  
boost::beast::string_view  
mime_type(boost::beast::string_view path)  
{  
	using boost::beast::iequals;  
	const auto ext = [&path]  
	{  
		auto const pos = path.rfind(".");  
		if (pos == boost::beast::string_view::npos)  
		{  
			return boost::beast::string_view{};  
		}  
		return path.substr(pos);  
	}();  
	if (iequals(ext, ".htm"))  
	{  
		return "text/html";  
	}  
	if (iequals(ext, ".html"))  
	{  
		return "text/html";  
	}  
	if (iequals(ext, ".php"))  
	{  
		return "text/html";  
	}  
	if (iequals(ext, ".css"))  
	{  
		return "text/css";  
	}  
	if (iequals(ext, ".txt"))  
	{  
		return "text/plain";  
	}  
	if (iequals(ext, ".js"))  
	{  
		return "application/javascript";  
	}  
	if (iequals(ext, ".json"))  
	{  
		return "application/json";  
	}  
	if (iequals(ext, ".xml"))  
	{  
		return "application/xml";  
	}  
	if (iequals(ext, ".swf"))  
	{  
		return "application/x-shockwave-flash";  
	}  
	if (iequals(ext, ".flv"))  
	{  
		return "video/x-flv";  
	}  
	if (iequals(ext, ".png"))  
	{  
		return "image/png";  
	}  
	if (iequals(ext, ".jpe"))  
	{  
		return "image/jpeg";  
	}  
	if (iequals(ext, ".jpeg"))  
	{  
		return "image/jpeg";  
	}  
	if (iequals(ext, ".jpg"))  
	{  
		return "image/jpeg";  
	}  
	if (iequals(ext, ".gif"))  
	{  
		return "image/gif";  
	}  
	if (iequals(ext, ".bmp"))  
	{  
		return "image/bmp";  
	}  
	if (iequals(ext, ".ico"))  
	{  
		return "image/vnd.microsoft.icon";  
	}  
	if (iequals(ext, ".tiff"))  
	{  
		return "image/tiff";  
	}  
	if (iequals(ext, ".tif"))  
	{  
		return "image/tiff";  
	}  
	if (iequals(ext, ".svg"))  
	{  
		return "image/svg+xml";  
	}  
	if (iequals(ext, ".svgz"))  
	{  
		return "image/svg+xml";  
	}  
	return "application/text";  
}  
  
// Append an HTTP rel-path to a local filesystem path.  
// The returned path is normalized for the platform.  
std::string  
path_cat(  
	boost::beast::string_view base,  
	boost::beast::string_view path)  
{  
	if (base.empty())  
	{  
		return path.to_string();  
	}  
	std::string result = base.to_string();  
#if BOOST_MSVC  
	constexpr char path_separator = '\\';  
	if (result.back() == path_separator)  
		result.resize(result.size() - 1);  
	result.append(path.data(), path.size());  
	for (auto& c : result)  
		if (c == '/')  
			c = path_separator;  
#else  
	constexpr char path_separator = '/';  
	if (result.back() == path_separator)  
	{  
		result.resize(result.size() - 1);  
	}  
	result.append(path.data(), path.size());  
#endif  
	return result;  
}  
  
// This function produces an HTTP response for the given  
// request. The type of the response object depends on the  
// contents of the request, so the interface requires the  
// caller to pass a generic lambda for receiving the response.  
template<  
	class Body, class Allocator,  
	class Send>  
	void  
	handle_request(  
		boost::beast::string_view doc_root,  
		http::request<Body, http::basic_fields<Allocator>>&& req,  
		Send&& send)  
{  
	// Returns a bad request response  
	auto const bad_request =  
		[&req](boost::beast::string_view why)  
	{  
		http::response<http::string_body> res{ http::status::bad_request, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = why.to_string();  
		res.prepare_payload();  
		return res;  
	};  
  
	// Returns a not found response  
	auto const not_found =  
		[&req](boost::beast::string_view target)  
	{  
		http::response<http::string_body> res{ http::status::not_found, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "The resource '" + target.to_string() + "' was not found.";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Returns a server error response  
	auto const server_error =  
		[&req](boost::beast::string_view what)  
	{  
		http::response<http::string_body> res{ http::status::internal_server_error, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "An error occurred: '" + what.to_string() + "'";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Make sure we can handle the method  
	if (req.method() != http::verb::get &&  
		req.method() != http::verb::head &&  
		req.method() != http::verb::post)  
	{  
		return send(bad_request("Unknown HTTP-method"));  
	}  
  
	// Request path must be absolute and not contain "..".  
	if (req.target().empty() ||  
		req.target()[0] != '/' ||  
		req.target().find("..") != boost::beast::string_view::npos)  
	{  
		return send(bad_request("Illegal request-target"));  
	}  
  
	// Build the path to the requested file  
	std::string path = path_cat(doc_root, req.target());  
	if (req.target().back() == '/')  
	{  
		path.append("index.html");  
	}  
  
	// In case of POST request, check the path URI  
	else if (req.target().front() == '/' && req.target().size() > 1)  
	{  
		using boost::beast::iequals;  
		const auto ext = [&path]  
		{  
			const auto pos = path.rfind(".");  
			if (pos == boost::beast::string_view::npos)  
			{  
				return send(bad_request("Illegal request-target"));  
			}  
			const auto pos2 = path.find(" ");  
			if (pos != boost::beast::string_view::npos)  
			{  
				return send(bad_request("Illegal request-target"));  
			}  
		}();  
		if (!iequals(ext, ".cgi") && !iequals(ext, ".exe"))  
		{  
			return send(bad_request("Illegal request-target"));  
		}  
	}  
  
	// Attempt to open the file  
	boost::beast::error_code ec;  
	http::file_body::value_type body;  
	body.open(path.c_str(), boost::beast::file_mode::scan, ec);  
  
	// Handle the case where the file doesn't exist  
	if (ec == boost::system::errc::no_such_file_or_directory)  
	{  
		return send(not_found(req.target()));  
	}  
  
	// Handle an unknown error  
	if (ec)  
	{  
		return send(server_error(ec.message()));  
	}  
  
	// Respond to HEAD request  
	if (req.method() == http::verb::head)  
	{  
		http::response<http::empty_body> res{ http::status::ok, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to GET request  
	else if (req.method() == http::verb::get)  
	{  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to POST request  
	else if (req.method() == http::verb::post)  
	{  
		boost::beast::string_view content_type = req[http::field::content_type];  
		if (content_type != "multipart/form-data" && content_type != "application/x-www-form-urlencoded")  
		{  
			return send(bad_request("Bad request"));  
		}  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
	}  
	return send(std::move(res));  
}  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(boost::system::error_code ec, char const* what)  
{  
	std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Handles an HTTP server connection  
class session : public std::enable_shared_from_this<session>  
{  
	// This is the C++11 equivalent of a generic lambda.  
	// The function object is used to send an HTTP message.  
	struct send_lambda  
	{  
		session& self_;  
  
		explicit  
			send_lambda(session& self)  
			: self_(self)  
		{  
		}  
  
		template<bool isRequest, class Body, class Fields>  
		void  
			operator()(http::message<isRequest, Body, Fields>&& msg) const  
		{  
			// The lifetime of the message has to extend  
			// for the duration of the async operation so  
			// we use a shared_ptr to manage it.  
			auto sp = std::make_shared<  
				http::message<isRequest, Body, Fields>>(std::move(msg));  
  
			// Store a type-erased version of the shared  
			// pointer in the class to keep it alive.  
			self_.res_ = sp;  
  
			// Write the response  
			http::async_write(  
				self_.socket_,  
				*sp,  
				boost::asio::bind_executor(  
					self_.strand_,  
					std::bind(  
						&session::on_write,  
						self_.shared_from_this(),  
						std::placeholders::_1,  
						std::placeholders::_2,  
						sp->need_eof())));  
		}  
	};  
  
	tcp::socket socket_;  
	boost::asio::strand<  
		boost::asio::io_context::executor_type> strand_;  
	boost::beast::flat_buffer buffer_;  
	std::string const& doc_root_;  
	http::request<http::string_body> req_;  
	std::shared_ptr<void> res_;  
	send_lambda lambda_;  
  
public:  
	// Take ownership of the socket  
	explicit  
		session(  
			tcp::socket socket,  
			std::string const& doc_root)  
		: socket_(std::move(socket))  
		, strand_(socket_.get_executor())  
		, doc_root_(doc_root)  
		, lambda_(*this)  
	{  
	}  
  
	// Start the asynchronous operation  
	void  
		run()  
	{  
		do_read();  
	}  
  
	void  
		do_read()  
	{  
		// Read a request  
		http::async_read(socket_, buffer_, req_,  
			boost::asio::bind_executor(  
				strand_,  
				std::bind(  
					&session::on_read,  
					shared_from_this(),  
					std::placeholders::_1,  
					std::placeholders::_2)));  
	}  
  
	void  
		on_read(  
			boost::system::error_code ec,  
			std::size_t bytes_transferred)  
	{  
		boost::ignore_unused(bytes_transferred);  
  
		// This means they closed the connection  
		if (ec == http::error::end_of_stream)  
			return do_close();  
  
		if (ec)  
			return fail(ec, "read");  
  
		// Send the response  
		handle_request(doc_root_, std::move(req_), lambda_);  
	}  
  
	void  
		on_write(  
			boost::system::error_code ec,  
			std::size_t bytes_transferred,  
			bool close)  
	{  
		boost::ignore_unused(bytes_transferred);  
  
		if (ec)  
			return fail(ec, "write");  
  
		if (close)  
		{  
			// This means we should close the connection, usually because  
			// the response indicated the "Connection: close" semantic.  
			return do_close();  
		}  
  
		// We're done with the response so delete it  
		res_ = nullptr;  
  
		// Read another request  
		do_read();  
	}  
  
	void  
		do_close()  
	{  
		// Send a TCP shutdown  
		boost::system::error_code ec;  
		socket_.shutdown(tcp::socket::shutdown_send, ec);  
  
		// At this point the connection is closed gracefully  
	}  
};  
  
//------------------------------------------------------------------------------  
  
// Accepts incoming connections and launches the sessions  
class listener : public std::enable_shared_from_this<listener>  
{  
	tcp::acceptor acceptor_;  
	tcp::socket socket_;  
	std::string const& doc_root_;  
  
public:  
	listener(  
		boost::asio::io_context& ioc,  
		tcp::endpoint endpoint,  
		std::string const& doc_root)  
		: acceptor_(ioc)  
		, socket_(ioc)  
		, doc_root_(doc_root)  
	{  
		boost::system::error_code ec;  
  
		// Open the acceptor  
		acceptor_.open(endpoint.protocol(), ec);  
		if (ec)  
		{  
			fail(ec, "open");  
			return;  
		}  
  
		// Bind to the server address  
		acceptor_.bind(endpoint, ec);  
		if (ec)  
		{  
			fail(ec, "bind");  
			return;  
		}  
  
		// Start listening for connections  
		acceptor_.listen(  
			boost::asio::socket_base::max_listen_connections, ec);  
		if (ec)  
		{  
			fail(ec, "listen");  
			return;  
		}  
	}  
  
	// Start accepting incoming connections  
	void  
		run()  
	{  
		if (!acceptor_.is_open())  
			return;  
		do_accept();  
	}  
  
	void  
		do_accept()  
	{  
		acceptor_.async_accept(  
			socket_,  
			std::bind(  
				&listener::on_accept,  
				shared_from_this(),  
				std::placeholders::_1));  
	}  
  
	void  
		on_accept(boost::system::error_code ec)  
	{  
		if (ec)  
		{  
			fail(ec, "accept");  
		}  
		else  
		{  
			// Create the session and run it  
			std::make_shared<session>(  
				std::move(socket_),  
				doc_root_)->run();  
		}  
  
		// Accept another connection  
		do_accept();  
	}  
};  
  
#endif  
```  
The server code should be in one class, probably.  I'll try that if I can figure out how I want it and what I can do.

---

## Comment 20

> Username: vinniefalco  
> Created at: 2018-01-04 20:11:49 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355386342  

You are storing `char*` in the client, what if those strings are invalidated?

---

## Comment 21

> Username: DragonOsman  
> Created at: 2018-01-05 10:29:23 UTC  
> Updated at: 2018-01-05 10:31:03 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355522470  

Would it be good to not use a char* then, or should I just check for nullptr?    
    
And what should I do about the version?  Should it be an int, or is there something special I can store it in?

---

## Comment 22

> Username: vinniefalco  
> Created at: 2018-01-05 13:39:50 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355556783  

You might want `std::string`. Version should be 11 (for HTTP/1.1).

---

## Comment 23

> Username: DragonOsman  
> Created at: 2018-01-06 12:10:52 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355742960  

Yeah, I'll use that.  I'll use elements of elements of argv[] which are char*s to initialize the std::string objects, then.    
  
As for the version.  I'll have to read the code in the version header file I guess.  I know the version is an int (10 or 11), but I'll still have to look at that header first.

---

## Comment 24

> Username: DragonOsman  
> Created at: 2018-01-06 17:09:54 UTC  
> Updated at: 2018-01-06 20:47:11 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355760757  

Another question: to start the application and the server when a user navigates to the application, do I hav to call `listener::run()`?  And what else do I aside from constructor a listener class object?  I also need a session class object as well, right?  
  
Will you never directly support HTTP/2 in Beast?  Most of the modern web browsers already support it, so I think more web apps and websites need to be developed that also support it.    
    
Edit: In my client class, how do I take care of these?  
```cpp  
// The io_context is required for all I/O  
boost::asio::io_context ioc;  
  
// These objects perform our I/O  
tcp::resolver resolver{ioc};  
tcp::socket socket{ioc};  
  
// Look up the domain name  
auto const results = resolver.resolve(host, port);  
  
// Make the connection on the IP address we get from a lookup  
boost::asio::connect(socket, results.begin(), results.end());  
  
// Set up an HTTP GET request message  
http::request<http::string_body> req{http::verb::get, target, version};  
req.set(http::field::host, host);  
req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
// Send the HTTP request to the remote host  
http::write(socket, req);  
  
// This buffer is used for reading and must be persisted  
boost::beast::flat_buffer buffer;  
  
// Declare a container to hold the response  
http::response<http::dynamic_body> res;  
  
// Receive the HTTP response  
http::read(socket, buffer, res);  
```  
My server is pretty much the same, except split between a .h and .cpp file now.

---

## Comment 25

> Username: vinniefalco  
> Created at: 2018-01-06 17:37:18 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355762445  

Hmm... the code is fairly self explanatory, the sessions are created by the listener as needed to handle each incoming connection.  
  
I wouldn't say that HTTP/2 will never be supported, the message model can handle it. But it would require a stream wrapper and won't work with the free functions due to the way the HTTP/2 protocol is specified.

---

## Comment 26

> Username: DragonOsman  
> Created at: 2018-01-07 11:02:23 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355814475  

Where do I put that code, though?  The client class constructor?  Or maybe a function named "run", or something like that?

---

## Comment 27

> Username: vinniefalco  
> Created at: 2018-01-07 17:46:06 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355839335  

What about `main`?

---

## Comment 28

> Username: DragonOsman  
> Created at: 2018-01-07 23:13:51 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355861067  

But shouldn't users have a way to let the library take care of that stuff?  If I'm right a HTTP client, I think stuff request handling should be up to the library.

---

## Comment 29

> Username: djarek  
> Created at: 2018-01-07 23:34:33 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355862211  

@DragonOsman   
Beast is a foundation and a low level library, i.e. a library upon which you build frameworks.  
  
Think of the C++ standard library - it has mostly barebones stuff. This is not due to laziness, but rather due to the fact that abstractions have this nasty property that, if they aren't well designed, they limit the user to some approach that the author/designer chose. Beast is designed to be on a similar level of "genericness" as `std::vector` or `std::istream` (sort of). Of course the trade-off is that it requires more boilerplate to get going. There's hope that at some point frameworks based on Beast (and using well designed abstractions) will be created to help avoid writing this boilerplate.

---

## Comment 30

> Username: DragonOsman  
> Created at: 2018-01-07 23:48:24 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355862990  

I know that.  What I'm saying is that the client and server libraries written using Beast should take care of all of the server and client stuff on their own.  Not that Beast should have it built in.

---

## Comment 31

> Username: vinniefalco  
> Created at: 2018-01-08 02:46:05 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355875382  

>the client and server libraries written using Beast should take care of all of the server and client stuff on their own  
  
The examples all function as advertised, no?

---

## Comment 32

> Username: DragonOsman  
> Created at: 2018-01-08 10:15:50 UTC  
> Updated at: 2018-01-08 10:16:04 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-355927384  

Yes, but the synchronous client puts its code in main.  I want to try to put the actual client functionality all in the client itself so that the application code doesn't need to worry about anything except how to use the client.  That's what I'm saying.

---

## Comment 33

> Username: DragonOsman  
> Created at: 2018-01-08 16:13:23 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-356012179  

I have another question.    
  
In this code: `session current_session{ socket, "." };`, I'm getting an error saying that the `boost::asio::basic_stream_socket<Pocket>::basic_stream_socket(const boost::asio::basic_stream_socket<boost::asio::ip::tcp> &) throw()` [with Protocol=boost::asio::ip::tcp] (declared implicitly) cannot be referenced - it is a deleted function.  Why is this and how do I fix it?  
  
Also, is the "target" variable meant to be the doc root entered to the application as a command-line argument, or is it the target page at the remote host we want to send a request to using the client?

---

## Comment 34

> Username: vinniefalco  
> Created at: 2018-01-08 18:31:47 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-356052900  

You are trying to make a copy of `socket`, which is non-copyable. You need to pass it as an rvalue-reference. Try writing `std::move(socket)` instead of `socket`.  
  
The `target` variable is set as the request-target in the outgoing HTTP GET request message.

---

## Comment 35

> Username: DragonOsman  
> Created at: 2018-01-08 19:26:45 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-356069695  

So how do I get the document root?  Should that be one of the command-line arguments?

---

## Comment 36

> Username: vinniefalco  
> Created at: 2018-01-08 19:41:13 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-356073713  

>So how do I get the document root?  
For the client?

---

## Comment 37

> Username: DragonOsman  
> Created at: 2018-01-08 19:49:53 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-356076122  

The document root where the application is deployed.  Like how if I launch it from my own computer, the document root would be the current directory.

---

## Comment 38

> Username: vinniefalco  
> Created at: 2018-01-08 20:01:32 UTC  
> Updated at: 2018-01-08 20:01:51 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-356079336  

The "document root" is the configurable location from which the HTTP server delivers its files. In the Beast server examples, this is either an absolute or a path relative to the current directory (which is not always where the executable is located). The Beast client examples do not have a document root, because they do not serve files.  
  
It sounds to me like you are asking "how can I determine the directory containing the executable" - that is beyond the scope of Beast. I suggest looking at `boost::filesystem` or consulting the documentation for your operating system.  
  
See:  
https://httpd.apache.org/docs/2.4/urlmapping.html#documentroot

---

## Comment 39

> Username: DragonOsman  
> Created at: 2018-01-08 20:13:59 UTC  
> Updated at: 2018-01-08 20:14:38 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-356082633  

I want to configure and launch the server.  That's why I'm asking.  Since the session constructor takes the document root is an argument.

---

## Comment 40

> Username: vinniefalco  
> Created at: 2018-01-08 20:48:21 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-356091650  

If you run the example program with no command line arguments, it will print help text. See:  
  
https://github.com/boostorg/beast/blob/6f08814a0c291eb9f03aaa1daefffdc45c1b9087/example/advanced/server/advanced_server.cpp

---

## Comment 41

> Username: DragonOsman  
> Created at: 2018-01-09 16:53:21 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-356344631  

I have this in main right now:  
```cpp  
int main(int argc, char* argv[])  
{  
	if (argc < 5)  
	{  
		std::cerr << "Usage: application.exe <address> <port> <doc_root> <threads>\n" <<  
			"Example:\n" <<  
			"    application.exe 0.0.0.0 8080 . 1\n";  
		return EXIT_FAILURE;  
	}  
	const auto address = boost::asio::ip::make_address(argv[1]);  
	const auto port = static_cast<unsigned short>(std::atoi(argv[2]));  
	std::string const doc_root = argv[3];  
	const auto threads = std::max<int>(1, std::atoi(argv[4]));  
  
	boost::asio::io_context ioc;  
  
	// The io_context is required for all I/O  
	boost::asio::io_context ioc{ threads };  
  
	// Create and launch a listening port  
	std::make_shared<listener>(  
		ioc,  
		tcp::endpoint{ address, port },  
		doc_root)->run();  
  
	// Run the I/O service on the requested number of threads  
	std::vector<std::thread> v;  
	v.reserve(threads - 1);  
	for (auto i = threads - 1; i > 0; --i)  
		v.emplace_back(  
			[&ioc]  
	{  
		ioc.run();  
	});  
	ioc.run();  
}  
```  
The server will run with this, right?  So now, when I get POSTed data from the HTML form and I want to pass it into my conversion function, where do I call said function?  After `io.run();`?

---

## Comment 42

> Username: vinniefalco  
> Created at: 2018-01-09 17:03:46 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-356348000  

>The server will run with this, right? So now, when I get POSTed data from the HTML form and I want to pass it into my conversion function, where do I call said function? After io.run();  
  
You are asking "how do I handle a POST request in my server?" All of the Beast example HTTP servers have the same structure: requests are handled in the function `handle_request`. I thought you understood this, you showed your changes to `handle_request` which check `if (req.method() == http::verb::post)`.  
  
It seems like you're asking basic questions related to the fundamentals of asynchronous programming. I should point out that Beast requires knowledge of Boost.Asio. In particular, you should already know how to write asynchronous programs using Asio.

---

## Comment 43

> Username: DragonOsman  
> Created at: 2018-01-09 20:05:04 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-356398403  

That's not necessarily what I was asking.  I was asking how to access the submitted data from the form in the .cpp file and where to add the call to the currency conversion function I'm writing.

---

## Comment 44

> Username: vinniefalco  
> Created at: 2018-01-09 21:38:24 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-356422437  

>I was asking how to access the submitted data from the form  
  
It will be in the message body, call `message::body()` to access it.

---

## Comment 45

> Username: DragonOsman  
> Created at: 2018-01-11 17:10:53 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-356995692  

I have linker errors on my project from Beast.    
  
> application.obj : error LNK2019: unresolved external symbol "void __cdecl fail(class boost::system::error_code,char const *)" (?fail@@YAXVerror_code@system@boost@@PBD@Z) referenced in function "public: __thiscall listener::listener(class boost::asio::io_context &,class boost::asio::ip::basic_endpoint<class boost::asio::ip::tcp>,class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const &)" (??0listener@@QAE@AAVio_context@asio@boost@@V?$basic_endpoint@Vtcp@ip@asio@boost@@@ip@23@ABV?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@@Z)  
> 1>application.obj : error LNK2019: unresolved external symbol "public: void __thiscall session::run(void)" (?run@session@@QAEXXZ) referenced in function "public: void __thiscall listener::on_accept(class boost::system::error_code)" (?on_accept@listener@@QAEXVerror_code@system@boost@@@Z)  
  
So what should I do here?  IDE is VS2017.

---

## Comment 46

> Username: vinniefalco  
> Created at: 2018-01-11 17:54:19 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357008210  

Try reading here:  
https://www.cprogramming.com/tutorial/compiler_linker_errors.html

---

## Comment 47

> Username: DragonOsman  
> Created at: 2018-01-11 18:15:21 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357014309  

Yes, but why did it flag `session::run()` and that call to `fail()` in `session::session`?  I know linker errors come up when the linker can't find a definition for a function you try to use.  But what exactly did I do warrant that?  
  
Here's my server code:  
```cpp  
#ifndef HTTP_SERVER_H  
#define HTTP_SERVER_H  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/asio/strand.hpp>  
  
using boost::asio::ip::tcp;  
namespace http = boost::beast::http;    // from <boost/beast/http.hpp>  
  
  
// Return a reasonable mime type based on the extension of a file.  
boost::beast::string_view mime_type(boost::beast::string_view path);  
std::string path_cat(boost::beast::string_view base,boost::beast::string_view path);  
void fail(boost::system::error_code ec, char const* what);  
  
// Handles an HTTP server connection  
class session : public std::enable_shared_from_this<session>  
{  
	// This is the C++11 equivalent of a generic lambda.  
	// The function object is used to send an HTTP message.  
	struct send_lambda  
	{  
		session& self_;  
  
		explicit  
			send_lambda(session& self)  
			: self_{ self }  
		{  
		}  
  
		template<bool isRequest, class Body, class Fields>  
		void operator()(http::message<isRequest, Body, Fields>&& msg) const  
		{  
			// The lifetime of the message has to extend  
			// for the duration of the async operation so  
			// we use a shared_ptr to manage it.  
			auto sp = std::make_shared<http::message<isRequest, Body, Fields>>(std::move(msg));  
  
			// Store a type-erased version of the shared  
			// pointer in the class to keep it alive.  
			self_.res_ = sp;  
  
			// Write the response  
			http::async_write(self_.socket_, *sp, boost::asio::bind_executor(self_.strand_,  
				std::bind(&session::on_write, self_.shared_from_this(), std::placeholders::_1, std::placeholders::_2, sp->need_eof())));  
		}  
	};  
  
	tcp::socket socket_;  
	boost::asio::strand<boost::asio::io_context::executor_type> strand_;  
	boost::beast::flat_buffer buffer_;  
	std::string const& doc_root_;  
	http::request<http::string_body> req_;  
	std::shared_ptr<void> res_;  
	send_lambda lambda_;  
  
public:  
	// Take ownership of the socket  
	explicit session(tcp::socket socket, std::string const& doc_root)  
		: socket_{ std::move(socket) }  
		, strand_{ socket_.get_executor() }  
		, doc_root_{ doc_root }  
		, lambda_{ *this }  
	{  
	}  
  
	// Start the asynchronous operation  
	void run();  
  
	void do_read();  
  
	void on_read(boost::system::error_code ec, std::size_t bytes_transferred);  
  
	void on_write(boost::system::error_code ec, std::size_t bytes_transferred, bool close);  
  
	void do_close();  
};  
  
// This function produces an HTTP response for the given  
// request. The type of the response object depends on the  
// contents of the request, so the interface requires the  
// caller to pass a generic lambda for receiving the response.  
template<  
	class Body, class Allocator,  
	class Send>  
void handle_request(boost::beast::string_view doc_root, http::request<Body, http::basic_fields<Allocator>>&& req, Send&& send)  
{  
	// Returns a bad request response  
	auto const bad_request =  
		[&req](boost::beast::string_view why)  
	{  
		http::response<http::string_body> res{ http::status::bad_request, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = why.to_string();  
		res.prepare_payload();  
		return res;  
	};  
  
	// Returns a not found response  
	auto const not_found =  
		[&req](boost::beast::string_view target)  
	{  
		http::response<http::string_body> res{ http::status::not_found, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "The resource '" + target.to_string() + "' was not found.";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Returns a server error response  
	auto const server_error =  
		[&req](boost::beast::string_view what)  
	{  
		http::response<http::string_body> res{ http::status::internal_server_error, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "text/html");  
		res.keep_alive(req.keep_alive());  
		res.body() = "An error occurred: '" + what.to_string() + "'";  
		res.prepare_payload();  
		return res;  
	};  
  
	// Make sure we can handle the method  
	if (req.method() != http::verb::get &&  
		req.method() != http::verb::head &&  
		req.method() != http::verb::post)  
	{  
		return send(bad_request("Unknown HTTP-method"));  
	}  
  
	// Request path must be absolute and not contain "..".  
	if (req.target().empty() ||  
		req.target()[0] != '/' ||  
		req.target().find("..") != boost::beast::string_view::npos)  
	{  
		return send(bad_request("Illegal request-target"));  
	}  
  
	// Build the path to the requested file  
	std::string path = path_cat(doc_root, req.target());  
	if (req.target().back() == '/')  
	{  
		path.append("index.html");  
	}  
  
	// In case of POST request, check the path URI  
	else if (req.target().front() == '/' && req.target().size() > 1)  
	{  
		using boost::beast::iequals;  
		const auto ext = [&path]  
		{  
			const auto pos = path.rfind(".");  
			if (pos == boost::beast::string_view::npos)  
			{  
				return send(bad_request("Illegal request-target"));  
			}  
			const auto pos2 = path.find(" ");  
			if (pos != boost::beast::string_view::npos)  
			{  
				return send(bad_request("Illegal request-target"));  
			}  
		}();  
		if (!iequals(ext, ".cgi") && !iequals(ext, ".exe"))  
		{  
			return send(bad_request("Illegal request-target"));  
		}  
	}  
  
	// Attempt to open the file  
	boost::beast::error_code ec;  
	http::file_body::value_type body;  
	body.open(path.c_str(), boost::beast::file_mode::scan, ec);  
  
	// Handle the case where the file doesn't exist  
	if (ec == boost::system::errc::no_such_file_or_directory)  
	{  
		return send(not_found(req.target()));  
	}  
  
	// Handle an unknown error  
	if (ec)  
	{  
		return send(server_error(ec.message()));  
	}  
  
	// Respond to HEAD request  
	if (req.method() == http::verb::head)  
	{  
		http::response<http::empty_body> res{ http::status::ok, req.version() };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to GET request  
	else if (req.method() == http::verb::get)  
	{  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to POST request  
	else if (req.method() == http::verb::post)  
	{  
		boost::beast::string_view content_type = req[http::field::content_type];  
		if (content_type != "multipart/form-data" && content_type != "application/x-www-form-urlencoded")  
		{  
			return send(bad_request("Bad request"));  
		}  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
	}  
	return send(std::move(res));  
}  
  
// Accepts incoming connections and launches the sessions  
class listener : public std::enable_shared_from_this<listener>  
{  
	tcp::acceptor acceptor_;  
	tcp::socket socket_;  
	std::string const& doc_root_;  
  
public:  
	listener(  
		boost::asio::io_context& ioc,  
		tcp::endpoint endpoint,  
		std::string const& doc_root)  
		: acceptor_{ ioc }  
		, socket_{ ioc }  
		, doc_root_{ doc_root }  
	{  
		boost::system::error_code ec;  
  
		// Open the acceptor  
		acceptor_.open(endpoint.protocol(), ec);  
		if (ec)  
		{  
			fail(ec, "open");  
			return;  
		}  
  
		// Bind to the server address  
		acceptor_.bind(endpoint, ec);  
		if (ec)  
		{  
			fail(ec, "bind");  
			return;  
		}  
  
		// Start listening for connections  
		acceptor_.listen(boost::asio::socket_base::max_listen_connections, ec);  
		if (ec)  
		{  
			fail(ec, "listen");  
			return;  
		}  
	}  
  
	// Start accepting incoming connections  
	void run()  
	{  
		if (!acceptor_.is_open())  
		{  
			return;  
		}  
		do_accept();  
	}  
  
	void do_accept()  
	{  
		acceptor_.async_accept(socket_, std::bind(&listener::on_accept, shared_from_this(), std::placeholders::_1));  
	}  
  
	void on_accept(boost::system::error_code ec)  
	{  
		if (ec)  
		{  
			fail(ec, "accept");  
		}  
		else  
		{  
			// Create the session and run it  
			std::make_shared<session>(std::move(socket_), doc_root_)->run();  
		}  
  
		// Accept another connection  
		do_accept();  
	}  
};  
  
#endif  
```  
and   
```cpp  
#include "http_server.h"  
#include <boost/beast/version.hpp>  
#include <boost/asio/bind_executor.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/config.hpp>  
#include <algorithm>  
#include <cstdlib>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
#include <vector>  
  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
namespace http = boost::beast::http;    // from <boost/beast/http.hpp>  
  
										// Return a reasonable mime type based on the extension of a file.  
boost::beast::string_view  
mime_type(boost::beast::string_view path)  
{  
	using boost::beast::iequals;  
	const auto ext = [&path]  
	{  
		auto const pos = path.rfind(".");  
		if (pos == boost::beast::string_view::npos)  
		{  
			return boost::beast::string_view{};  
		}  
		return path.substr(pos);  
	}();  
	if (iequals(ext, ".htm"))  
	{  
		return "text/html";  
	}  
	if (iequals(ext, ".html"))  
	{  
		return "text/html";  
	}  
	if (iequals(ext, ".php"))  
	{  
		return "text/html";  
	}  
	if (iequals(ext, ".css"))  
	{  
		return "text/css";  
	}  
	if (iequals(ext, ".txt"))  
	{  
		return "text/plain";  
	}  
	if (iequals(ext, ".js"))  
	{  
		return "application/javascript";  
	}  
	if (iequals(ext, ".json"))  
	{  
		return "application/json";  
	}  
	if (iequals(ext, ".xml"))  
	{  
		return "application/xml";  
	}  
	if (iequals(ext, ".swf"))  
	{  
		return "application/x-shockwave-flash";  
	}  
	if (iequals(ext, ".flv"))  
	{  
		return "video/x-flv";  
	}  
	if (iequals(ext, ".png"))  
	{  
		return "image/png";  
	}  
	if (iequals(ext, ".jpe"))  
	{  
		return "image/jpeg";  
	}  
	if (iequals(ext, ".jpeg"))  
	{  
		return "image/jpeg";  
	}  
	if (iequals(ext, ".jpg"))  
	{  
		return "image/jpeg";  
	}  
	if (iequals(ext, ".gif"))  
	{  
		return "image/gif";  
	}  
	if (iequals(ext, ".bmp"))  
	{  
		return "image/bmp";  
	}  
	if (iequals(ext, ".ico"))  
	{  
		return "image/vnd.microsoft.icon";  
	}  
	if (iequals(ext, ".tiff"))  
	{  
		return "image/tiff";  
	}  
	if (iequals(ext, ".tif"))  
	{  
		return "image/tiff";  
	}  
	if (iequals(ext, ".svg"))  
	{  
		return "image/svg+xml";  
	}  
	if (iequals(ext, ".svgz"))  
	{  
		return "image/svg+xml";  
	}  
	return "application/text";  
}  
  
// Append an HTTP rel-path to a local filesystem path.  
// The returned path is normalized for the platform.  
std::string path_cat(boost::beast::string_view base, boost::beast::string_view path)  
{  
	if (base.empty())  
	{  
		return path.to_string();  
	}  
	std::string result = base.to_string();  
#if BOOST_MSVC  
	constexpr char path_separator = '\\';  
	if (result.back() == path_separator)  
		result.resize(result.size() - 1);  
	result.append(path.data(), path.size());  
	for (auto& c : result)  
		if (c == '/')  
			c = path_separator;  
#else  
	constexpr char path_separator = '/';  
	if (result.back() == path_separator)  
	{  
		result.resize(result.size() - 1);  
	}  
	result.append(path.data(), path.size());  
#endif  
	return result;  
}  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void fail(boost::system::error_code ec, char const* what)  
{  
	std::cerr << what << ": " << ec.message() << "\n";  
}  
  
//------------------------------------------------------------------------------  
  
  
  
void session::run()  
{  
	do_read();  
}  
  
void session::do_read()  
{  
	// Read a request  
	http::async_read(socket_, buffer_, req_, boost::asio::bind_executor(  
		strand_, std::bind(&session::on_read, shared_from_this(), std::placeholders::_1, std::placeholders::_2)));  
}  
  
void session::on_read(boost::system::error_code ec, std::size_t bytes_transferred)  
{  
	boost::ignore_unused(bytes_transferred);  
  
	// This means they closed the connection  
	if (ec == http::error::end_of_stream)  
		return do_close();  
  
	if (ec)  
		return fail(ec, "read");  
  
	// Send the response  
	handle_request(doc_root_, std::move(req_), lambda_);  
}  
  
void session::on_write(boost::system::error_code ec, std::size_t bytes_transferred, bool close)  
{  
	boost::ignore_unused(bytes_transferred);  
  
	if (ec)  
		return fail(ec, "write");  
  
	if (close)  
	{  
		// This means we should close the connection, usually because  
		// the response indicated the "Connection: close" semantic.  
		return do_close();  
	}  
  
	// We're done with the response so delete it  
	res_ = nullptr;  
  
	// Read another request  
	do_read();  
}  
  
void session::do_close()  
{  
	// Send a TCP shutdown  
	boost::system::error_code ec;  
	socket_.shutdown(tcp::socket::shutdown_send, ec);  
  
	// At this point the connection is closed gracefully  
}  
```

---

## Comment 48

> Username: vinniefalco  
> Created at: 2018-01-11 18:38:55 UTC  
> Updated at: 2018-01-11 18:39:08 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357021108  

Did you remember to add the .cpp file to the project? Anyway, this questions is not related to Beast.

---

## Comment 49

> Username: DragonOsman  
> Created at: 2018-01-11 18:39:47 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357021369  

Yes, I did add it.

---

## Comment 50

> Username: DragonOsman  
> Created at: 2018-01-11 19:22:08 UTC  
> Updated at: 2018-01-11 19:25:46 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357033291  

For `message::body()`, how do I initialize the message variable?  I think it should be a request rather than a response if I'm accessing submitted data from a form into the CGI script (am I right?) but what about the body and fields template parameters?  And while I have to parse the body to get the things I want?  
  
Edit: And is the earlier question really not related to Beast?  I'm asking about linker errors generated by code using Beast.

---

## Comment 51

> Username: vinniefalco  
> Created at: 2018-01-11 20:24:45 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357050594  

> For message::body(), how do I initialize the message variable?  
  
Like this?  
```  
request<string_body> req;  
req.body() = "Hello, world!";  
```  
  
> I'm asking about linker errors generated by code using Beast.  
  
Link errors are always resolved using identical techniques no matter the library being used.

---

## Comment 52

> Username: DragonOsman  
> Created at: 2018-01-12 11:28:54 UTC  
> Updated at: 2018-01-12 11:44:43 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357214934  

The linker errors are confusing me.  I've defined the function `fail`, I have the .cpp file added to the project, and I don't see anything out of place in the way I'm using it in the `listener` class constructor either.  So what did I do wrong?  It's the same problem with `session::run()`.  I really don't get it.    
  
And how do I specify in the request object that I'm expecting a POST request to respond to?    
  
Edit: I tried putting everything back in the header file again, but now I have this compiler error (this is just the first one):  
>1>c:\users\osman\programming\visual_studio_2017\projects\cgi_programs\currency_converter\currency_converter\http_server.h(166): error C3493: 'send' cannot be implicitly captured because no default capture mode has been specified  
  
It also seems to be treating `send()` as a variable even though it seems like a function.  If it's a function, isn't there a way to specify the function call?

---

## Comment 53

> Username: vinniefalco  
> Created at: 2018-01-12 11:48:46 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357218806  

It seems to me that you're struggling with C++ in general, perhaps take a break and work on something a little easier before picking this back up?

---

## Comment 54

> Username: DragonOsman  
> Created at: 2018-01-12 15:18:32 UTC  
> Updated at: 2018-01-12 18:05:09 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357265546  

I got it to compile by doing this:  
```cpp  
else if (req.target().front() == '/' && req.target().size() > 1)  
{  
	using boost::beast::iequals;  
	const auto ext = [&path]  
	{  
		const auto pos = path.rfind(".");  
		if (pos == boost::beast::string_view::npos)  
		{  
			return boost::beast::string_view{};  
		}  
		const auto pos2 = path.find(" ");  
		if (pos != boost::beast::string_view::npos)  
		{  
			return boost::beast::string_view{};  
		}  
		return boost::beast::string_view(path.substr(pos));  
	}();  
	if (!iequals(ext, ".cgi") && !iequals(ext, ".exe"))  
	{  
		return send(bad_request("Illegal request-target"));  
	}  
}  
```  
Is this a good idea?    
  
Edit: Just noticed another mistake.  Will fix it.  
  
Edit: Okay, here:  
```cpp  
// In case of POST request, check the path URI  
else if (req.target().front() == '/' && req.target().size() > 1)  
{  
	using boost::beast::iequals;  
	const auto ext = [&path]  
	{  
		const auto pos = path.rfind(".");  
		if (pos == boost::beast::string_view::npos)  
		{  
			return boost::beast::string_view{};  
		}  
		pos = path.find(" ");  
		if (pos != boost::beast::string_view::npos)  
		{  
			return boost::beast::string_view{};  
		}  
		return boost::beast::string_view(path.substr(pos));  
	}();  
	if (!iequals(ext, ".cgi") && !iequals(ext, ".exe"))  
	{  
		return send(bad_request("Illegal request-target"));  
	}  
}  
```  
The empty `string_view` is a sentinel, right?  So I'm using it correctly here?

---

## Comment 55

> Username: DragonOsman  
> Created at: 2018-01-12 22:33:20 UTC  
> Updated at: 2018-01-12 23:40:25 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357373000  

I'm trying to call `message::body` like this:  
```cpp  
http::request<http::string_body> req = handle_request(boost::beast::string_view(doc_root), req, [=](http::request<http::string_body> &req) {  
		  
});  
```  
I need help with writing that lambda.  If that's okay.  I need to access POST data sent from an HTML form.  I think I can get it by checking what the method is and then extracting the message body inside an if-condition that evaluates to true for POST.  Correct me if I'm wrong.  
  
I'm getting an error on `handle_request()` saying that I can't give it these arguments, with the lambda being of type `void`.

---

## Comment 56

> Username: vinniefalco  
> Created at: 2018-01-12 23:46:16 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357385399  

Yeah I don't think you understand what `send` is for...

---

## Comment 57

> Username: DragonOsman  
> Created at: 2018-01-12 23:48:59 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357385831  

Yeah, not really.  All I can see is that it's used as a function to give a response back to the client.  Hence the third argument needing to be a function, and preferably a lambda.  If I understand correctly until that point at least.

---

## Comment 58

> Username: vinniefalco  
> Created at: 2018-01-13 01:52:48 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357400734  

Study the example to see what is passed for `send`.

---

## Comment 59

> Username: DragonOsman  
> Created at: 2018-01-13 11:32:27 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357428133  

I just see an object called `lambda_` being used for it in `session::on_read()`.  So what do I actually pass for that?  
  
Also, if I need to handle two POST requests from the client, one from a form and one from the client sending a variable to the server, would it be good to use `handle_request()` for both?  For the former, what I need is a money amount entered in an input tag and a from currency and a to currency.  For the latter it's HTML5 geolocation coordinates (I'll use an XMLHttpRequest object on the client side, but it seems like there's no way to handle that directly in C++).    
  
And do I need to pass the same kind of generic lambda as the one used in `handle_request()` for the different types of HTTP errors for `send`?

---

## Comment 60

> Username: DragonOsman  
> Created at: 2018-01-13 22:28:01 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357472230  

If it's okay, I'd like to have some help in understanding how to process HTTP requests in my application.  Also, from what you've so far of my code, would you say the server as it is now can handle CGI applications?

---

## Comment 61

> Username: vinniefalco  
> Created at: 2018-01-14 00:30:12 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357478602  

>If it's okay, I'd like to have some help in understanding how to process HTTP requests in my application  
  
I would start with the example server and just modify `handle_request` as needed. I don't understand why you think you need to mess with the `send` lambda.  
  
>Also, from what you've so far of my code, would you say the server as it is now can handle CGI applications?  
  
I have no idea about that.

---

## Comment 62

> Username: DragonOsman  
> Created at: 2018-01-14 12:03:57 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357507051  

I'm asking about the `send` lambda because I'm thinking I may have to call `handle_request()` in my application code to handle requests.  Unless I don't really need to call that function to respond to a request?

---

## Comment 63

> Username: vinniefalco  
> Created at: 2018-01-14 23:26:58 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357551888  

>Unless I don't really need to call that function to respond to a request?  
  
If you copy the example server, `handle_request` is called for you. If you want to call it yourself, then you need to do what the example code does in the send lambda.

---

## Comment 64

> Username: DragonOsman  
> Created at: 2018-01-15 14:37:31 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357700144  

Is there a way to control what happens in the request handling if I just let the server code call the function by itself?  If I get a variable and some form data via POST, how do I access it if I just let the server code itself call `handle_request()`?  I see that `handle_request()` is called in `session::on_read()`, so do I just call that?  What preparations do I need to make beforehand, though?

---

## Comment 65

> Username: DragonOsman  
> Created at: 2018-01-15 16:43:56 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357735055  

And when I want to call `session::on_read()`, how should I initialize the  `error_code` object and `bytes_transferred` object objects?  I see that the function doesn't accept reference arguments, so the values won't be changed inside the function.

---

## Comment 66

> Username: DragonOsman  
> Created at: 2018-01-15 17:27:20 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-357745446  

But wait, how can I process the request and get the data and objects I need from the client?  Can I do that with `on_read()`?  I need to get the money amount, currency and geolocation data from the client to the server and use it to do currency conversions.  So what do I do after calling `on_read()`?  
  
This is my `main` right now:  
```cpp  
int main(int argc, char* argv[])  
{  
	if (argc < 5)  
	{  
		std::cerr << "Usage: application.exe <address> <port> <doc_root> <threads>\n" <<  
			"Example:\n" <<  
			"    application.exe 0.0.0.0 8080 . 1\n";  
		return EXIT_FAILURE;  
	}  
	const auto address = boost::asio::ip::make_address(argv[1]);  
	const auto port = static_cast<unsigned short>(std::atoi(argv[2]));  
	const std::string doc_root = argv[3];  
	const auto threads = std::max<int>(1, std::atoi(argv[4]));  
  
	LoaderFile loader;  
	Template t{ loader };  
	t.load("index.html");  
	t.set("title", "DragonOsman Currency Converter");  
  
	// The io_context is required for all I/O  
	boost::asio::io_context ioc{ threads };  
  
	// Create and launch a listening port  
	std::make_shared<listener>(  
		ioc, tcp::endpoint{ address, port }, doc_root)->run();  
  
	// Run the I/O service on the requested number of threads  
	std::vector<std::thread> v;  
	v.reserve(threads - 1);  
	for (auto i = threads - 1; i > 0; --i)  
		v.emplace_back(  
			[&ioc]  
	{  
		ioc.run();  
	});  
	ioc.run();  
  
	boost::asio::ip::tcp::socket socket{ ioc };  
	session cur_session{ std::move(socket), doc_root };  
  
	boost::system::error_code ec;  
	std::size_t bytes_transferred;  
	cur_session.on_read(ec, bytes_transferred);  
}  
```  
Is it okay for `handle_request()` to be returning `send` even though it's a `void` function?

---

## Comment 67

> Username: DragonOsman  
> Created at: 2018-01-17 21:14:11 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-358447445  

If I call `session::on_read()`, what do I do after that to get the request body?

---

## Comment 68

> Username: DragonOsman  
> Created at: 2018-01-18 22:45:11 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-358806817  

Okay, never mind. I think it'll be better if I close this issue and open a new one to ask how to get the request body after calling `session::on_read()`.  After all, I think the original purpose of opening this issue was already taken care of.

---

## Comment 69

> Username: vinniefalco  
> Created at: 2019-01-14 21:51:58 UTC  
> Url: https://github.com/boostorg/beast/issues/958#issuecomment-454175370  

These questions have nothing to do with beast and furthermore they could be answered yourself with just a little effort.
