# #1150 - Need help handling XHR GET request from client-side code [Closed]

> Username: DragonOsman  
> Created at: 2018-06-04 20:52:07 UTC  
> Updated at: 2018-06-23 21:56:19 UTC  
> Closed at: 2018-06-23 21:56:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1150  

Hi, everyone.    
  
Please note: this is only a problem I have; it is not a bug report or anything like that.  
  
I have an environment variable set to the access key for the currency API I'm using for my currency convertor web app.  I need to get that key to my JavaScript code so I can use it there to request a list of currencies and populate two dropdowns with it in an HTML form.  That form submits to the server app via POST with a "to currency" and a money to convert.    
  
I think I may be handling the GET request for the access key wrong.  Here's my C++ code for reference: https://gist.github.com/DragonOsman/7bbdb049ea2a77173c5dafbecd5f1be2 and this is the JS code: https://gist.github.com/DragonOsman/c6e8fb15343544e662f474c5a526d1c2 .    
  
The problem is in the `handle_request` function, I think, so I'll post that here.    
```cpp  
template<class Body, class Allocator, class Send>  
void handle_request(boost::beast::string_view doc_root, http::request<Body, http::basic_fields<Allocator>>&& req, Send&& send)  
{  
	// Returns a bad request response  
	const auto bad_request = [&req](boost::beast::string_view why)  
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
	const auto not_found = [&req](boost::beast::string_view target)  
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
	const auto server_error = [&req](boost::beast::string_view what)  
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
	else if (req.target().front() == '/' && req.target().size() > 1 && req.method() == http::verb::post)  
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
			if (pos2 != boost::beast::string_view::npos)  
			{  
				return boost::beast::string_view{};  
			}  
			return boost::beast::string_view(path.substr(pos));  
		}();  
		if (!iequals(ext, ".exe") && !iequals(ext, ".cgi"))  
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
  
	// Respond to GET request for currency API access key from JavaScript code  
	else if (req.method() == http::verb::get && req.target() == "/?q=accesskey")  
	{  
		http::string_body::value_type str_body{ accesskey };  
		http::response<http::string_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(str_body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type("/q?=accesskey"));  
		res.content_length(str_body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Respond to POST request  
	else if (req.method() == http::verb::post)  
	{  
		boost::beast::string_view content_type = req[http::field::content_type];  
		if (content_type.find("multipart/form-data") == std::string::npos &&   
			content_type.find("application/x-www-form-urlencoded") == std::string::npos)  
		{  
			return send(bad_request("Bad request"));  
		}  
  
		std::map<std::string, std::string> parsed_value = parse(req.body());  
		std::cout << "POST body example:\n" << req.body() << '\n';  
		std::cout << "parsed_value map:\n";  
		for (const auto &x : parsed_value)  
		{  
			std::cout << x.first << ": " << x.second << '\n';  
		}  
		double money_amount = std::stod(parsed_value["currency_amount"]);  
		std::string to_currency = parsed_value["to_currency"];  
		std::cout << "Line 474: to_currency is: " << to_currency << '\n';  
		std::string currency_abbr = to_currency.substr(0, 3);  
		double conversion_result = convert(currency_abbr, money_amount);  
		  
		http::string_body::value_type str_body = std::to_string(conversion_result);  
		http::response<http::string_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(str_body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(str_body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
}  
```  
I have this error in the browser's console:  
```  
 HTTP500: SERVER ERROR - The server encountered an unexpected condition that prevented it from fulfilling the request.  
(XHR)GET - http://localhost:8080/?q=accesskey  
```  
So what did I do wrong?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-04 21:27:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-394505508  

Okay, well if `handle_request` is generating the response that means it is producing a 500 status. This is the value produced by `beast::http::status::internal_server_error`. Your `handle_request` produces this response in the lambda named `server_error`. This lambda is only invoked when `ec` is set after calling `open`, and the error is not a file or directory not found:  
  
```  
	// Handle an unknown error  
	if (ec)  
	{  
		return send(server_error(ec.message()));  
	}  
```  
  
The text "The server encountered an unexpected condition that prevented it from fulfilling the request" is not coming from Beast, it must be coming from your browser. However, Beast puts the error message text into the body of the response, as you can see here:  
  
```  
    res.body() = "An error occurred: '" + what.to_string() + "'";  
```  
  
It would be nice to see this text. You might need to adjust some browser debug setting to view the complete HTTP response. Even so, this information is sufficient to narrow down the problem considerably. Your problem is this line:  
  
```  
    body.open(path.c_str(), boost::beast::file_mode::scan, ec);  
```  
  
Your path is probably `"/?q=accesskey"`. I would imagine that no such file exists, and I would even go so far to say that perhaps "?" and/or "=" are invalid characters for the file system.  
  
I notice that you are checking for the access key query parameter **after** treating the target as a path and trying to open the file. I believe this is the source of your bug. Try moving the following block to **before** the `file_body` is constructed:  
  
```  
	if (req.method() == http::verb::get && req.target() == "/?q=accesskey")  
	{  
		...  
	}  
```  
  
I also notice something else. First you are checking if the method is GET, and then in the else clause you check if the method is GET and if the target matches a particular text:  
  
```  
        ...  
	else if (req.method() == http::verb::get)  
        ...  
	else if (req.method() == http::verb::get && req.target() == "/?q=accesskey")  
        ...  
```  
  
The second condition will never be evaluated, because if the verb is GET then the first `else` will always be `true`.  
  
You have several problems with the logic in your `handle_request`, resolving these errors will likely correct the behavior of your program.

---

## Comment 2

> Username: DragonOsman  
> Created at: 2018-06-04 23:13:23 UTC  
> Updated at: 2018-06-04 23:23:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-394529311  

Thanks for the reply.  
  
So if I shouldn't make two separate conditions for the GET case, how should I handle the second GET request?  Should I check for the request for the access key in the first GET request case?  If so, then I'm confused about how to code that part so that it'd correctly both the `/` path and the one for the access key.    
  
How do I make sure that both GET checks run, and that it handles the query string correctly?  
  
Edit: Also, I need confirmation for something.  I want to use Jinja2Cpp templates with my application, and according to the developer in this thread: https://github.com/flexferrum/Jinja2Cpp/issues/14 , I'll have to replace `file_body` in `handle_request` with `Jinja2::Template`.  Would that be a good idea?  I don't want to break anything.  I'll try it after I've fixed the current problem.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-06-05 02:28:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-394560707  

> So if I shouldn't make two separate conditions for the GET case, how should I handle the second GET request?  
  
First, check if the target matches the access key request. If that doesn't match, process the GET request as if it was a file.  
  
> How do I make sure that both GET checks run, and that it handles the query string correctly?  
  
This sounds like a general programming problem...you need to implement a simple "request router." This is a piece of code that examples the request and decides how to satisfy it.  
  
> according to the developer in this thread: flexferrum/Jinja2Cpp#14 , I'll have to replace file_body in handle_request with `Jinja2::Template`  
  
I have no idea what a `Jinja2::Template` is, but I have strong doubts that it meets the requirements of **Body**, specified in the Beast documentation:  
  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/concepts/Body.html  
  
Perhaps what you need to do is use the `Jinja2::Template` to produce a `std::string`, and then use a `string_body` instead of a `file_body` to send the response.

---

## Comment 4

> Username: flexferrum  
> Created at: 2018-06-05 09:24:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-394642810  

> Perhaps what you need to do is use the Jinja2::Template to produce a std::string, and then use a string_body instead of a file_body to send the response.  
  
That is exactly I have suggested.

---

## Comment 5

> Username: DragonOsman  
> Created at: 2018-06-05 17:04:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-394786524  

I'll try it.  But yeah, first I need to see if it meets the requirements of a **Body** specified in the Beast documentation.    
  
@vinniefalco I wonder if it'll work I put the response to the access key request before the construction of the `file_body` / `jinja2::Template` (whichever one I use) instance as you suggested before.  Because in that case, both GET check blocks should run, right?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-06-05 17:38:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-394797069  

Try it and see.

---

## Comment 7

> Username: DragonOsman  
> Created at: 2018-06-05 20:19:30 UTC  
> Updated at: 2018-06-05 20:20:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-394846260  

I tried changing the placement of that block of code.  But it didn't work completely as intended.  I'm putting `handle_request` here again:  
  
```cpp  
template<class Body, class Allocator, class Send>  
void handle_request(boost::beast::string_view doc_root, http::request<Body, http::basic_fields<Allocator>>&& req, Send&& send)  
{  
	// Returns a bad request response  
	const auto bad_request = [&req](boost::beast::string_view why)  
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
	const auto not_found = [&req](boost::beast::string_view target)  
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
	const auto server_error = [&req](boost::beast::string_view what)  
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
  
	if (req.method() == http::verb::get && req.target() == "/?q=accesskey")  
	{  
		http::string_body::value_type str_body{ accesskey };  
		http::response<http::string_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(str_body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type("/q?=accesskey"));  
		res.content_length(str_body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
  
	// Build the path to the requested file  
	std::string path = path_cat(doc_root, req.target());  
	if (req.target().back() == '/')  
	{  
		path.append("index.html");  
	}  
  
	// In case of POST request, check the path URI  
	/*else if (req.target().front() == '/' && req.target().size() > 1 && req.method() == http::verb::post)  
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
			if (pos2 != boost::beast::string_view::npos)  
			{  
				return boost::beast::string_view{};  
			}  
			return boost::beast::string_view(path.substr(pos));  
		}();  
		if (!iequals(ext, ".exe") && !iequals(ext, ".cgi"))  
		{  
			return send(bad_request("Illegal request-target"));  
		}  
	}*/  
  
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
  
	// Respond to GET request for currency API access key from JavaScript code  
	/*else if (req.method() == http::verb::get && req.target() == "/?q=accesskey")  
	{  
		http::string_body::value_type str_body{ accesskey };  
		http::response<http::string_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(str_body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type("/q?=accesskey"));  
		res.content_length(str_body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}*/  
  
	// Respond to POST request  
	else if (req.method() == http::verb::post)  
	{  
		boost::beast::string_view content_type = req[http::field::content_type];  
		if (content_type.find("multipart/form-data") == std::string::npos &&   
			content_type.find("application/x-www-form-urlencoded") == std::string::npos)  
		{  
			return send(bad_request("Bad request"));  
		}  
  
		std::map<std::string, std::string> parsed_value = parse(req.body());  
		std::cout << "POST body example:\n" << req.body() << '\n';  
		std::cout << "parsed_value map:\n";  
		for (const auto &x : parsed_value)  
		{  
			std::cout << x.first << ": " << x.second << '\n';  
		}  
		double money_amount = std::stod(parsed_value["currency_amount"]);  
		std::string to_currency = parsed_value["to_currency"];  
		std::cout << "Line 474: to_currency is: " << to_currency << '\n';  
		std::string currency_abbr = to_currency.substr(0, 3);  
		double conversion_result = convert(currency_abbr, money_amount);  
		  
		http::string_body::value_type str_body = std::to_string(conversion_result);  
		http::response<http::string_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(str_body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(str_body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
}  
```  
It seems like the client code isn't getting the access key at all.  How do I verify the target I'm trying to use? [I don't have a file by that name, so I'll be lost if I have to treat it as a file.]  
  
Here's the JS code if you want to see it: https://gist.github.com/DragonOsman/c6e8fb15343544e662f474c5a526d1c2

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-06-05 20:37:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-394851757  

Well, you need to step through `handle_request` using a debugger and inspect the values to determine they are what you think they are

---

## Comment 9

> Username: DragonOsman  
> Created at: 2018-06-06 16:47:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-395137045  

I tried it like this instead:  
```cpp  
// Respond to GET request  
else if (req.method() == http::verb::get)  
{  
	if (req.target() == "/index.html")  
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
	else  
	{  
		auto target = req.target();  
		std::cerr << "Line 472: Target is " << target << '\n';  
		http::string_body::value_type str_body{ accesskey };  
		http::response<http::string_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(str_body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "plain/text");  
		res.content_length(str_body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
}  
```  
The result of the `std::cerr << "Line 472: Target is " << target << '\n';` line is just   
```  
Line 472: Target is /  
```  
  
And then the program just exits.    
  
I'll try to debug it again.

---

## Comment 10

> Username: DragonOsman  
> Created at: 2018-06-06 17:57:30 UTC  
> Updated at: 2018-06-06 17:58:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-395159014  

In VS2017, when running the code through the debugger, I saw this exception being thrown:   
```  
Exception thrown at 0x00E386BC in currency_converter.exe: 0xC0000005: Access violation reading location 0x00000000.  
```  
It's in `std::char_traits<char>::length()`, in some library code.  It's called when code execution gets to line 473:  
```cpp  
http::string_body::value_type str_body{ accesskey };  
```  
Should I make a text file and put the access key in there, and then extract and return that when the client-side code asks for it?  If so, what URL should I send the GET request to from the client-side code?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2018-06-06 18:35:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-395170379  

I have no idea what is going on with your program, but it sounds like you are asking general programming questions and not Beast-specific questions.

---

## Comment 12

> Username: DragonOsman  
> Created at: 2018-06-06 23:29:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-395245588  

They're at least general web programming questions I guess.    
  
By the way, is there a HTML template library that could work well with Beast?  For instance, Jinja2Cpp?  I think it'd be good if the Body concept in Beast were to have support for HTML templates.  Just a suggestion.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2018-06-06 23:43:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-395247892  

>  I think it'd be good if the Body concept in Beast were to have support for HTML templates  
  
You have it exactly backwards. The point of having the **Body** concept is so that *other* libraries can be built on top of it to meet those requirements.

---

## Comment 14

> Username: DragonOsman  
> Created at: 2018-06-08 00:25:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-395607070  

So that means we need a new HTML template library made for Beast.  
  
Anyway, I think I solved the environment variable problem, but now it's downloading `8080/` when I navigate to `http://localhost:8080/` in my browser.  Why could it be doing this?  And what ways are there to fix it?  
  
I started having the downloading issue when I started trying to use environment variables.  Should I do something in `handle_request` to deal with this?  That's where it's parsing the URL, isn't it?

---

## Comment 15

> Username: vinniefalco  
> Created at: 2018-06-08 02:25:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-395625940  

> So that means we need a new HTML template library made for Beast.  
  
Well, I'm not sure that's necessary. If an HTML template library takes the template and produces a `std::string` then you can just use `http::string_body`. That is probably the easiest route. A "whole new library" should not be needed just to produce dynamic content.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2018-06-08 02:26:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-395626010  

>Why could it be doing this?  
  
Your `handle_request` is not doing the right thing in this case.

---

## Comment 17

> Username: DragonOsman  
> Created at: 2018-06-08 12:43:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-395749532  

So what does it have to do?  The library is parsing the URL, so I probably need to write code in `handle_request` to work with that better and ensure that no funny stuff is done to the URL.  Or is that wrong?

---

## Comment 18

> Username: vinniefalco  
> Created at: 2018-06-08 15:58:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-395806639  

I'm not sure what kind of answer you are expecting. Yes, you need to handle the request in the function `handle_request`. Yes, you will need to do some calculations on the URI to determine how to handle the request. The manner in which you implement that is up to you, it is a general programming problem (and not one that I can help with).

---

## Comment 19

> Username: DragonOsman  
> Created at: 2018-06-08 17:02:45 UTC  
> Updated at: 2018-06-08 17:04:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-395823936  

What part of the documentation and what header file in the Beast library would be best to read up on for this?  I'll try to read that and also ask about anything I don't get.  
  
As for HTML templates.  What if I could use a `file_body` with a Jinja2 template?  Open it as a file and put the template string there.  Is that possible in Beast if Jinja2Cpp allows it?  
  
Edit: I'm asking about that with the library because I was thinking that Beast may have an interface for helping parse URLs.

---

## Comment 20

> Username: vinniefalco  
> Created at: 2018-06-08 17:17:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-395827877  

> What part of the documentation and what header file in the Beast library would be best to read up on for this? Edit: I'm asking about that with the library because I was thinking that Beast may have an interface for helping parse URLs.  
  
Beast does not provide any tools for parsing the URI-target in requests (although it might be a feature that appears in the future).  
  
> As for HTML templates. What if I could use a file_body with a Jinja2 template? Open it as a file and put the template string there. Is that possible in Beast if Jinja2Cpp allows it?  
  
A `file_body` is for delivering all or part of a file as-is. You say you want to "put the template string there." What does that mean? My understanding is that Jina2Cpp takes as input a template and parameters and produces the rendered page as output into dynamically allocated storage. No file is involved in the final result. You can take the resulting dynamic content and send it directly in Beast without resorting to `file_body`, by using one of the other body types.

---

## Comment 21

> Username: DragonOsman  
> Created at: 2018-06-10 17:59:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-396068637  

I was under the impression that Beast may have a facility for parsing URLs since it does help parse the main request line and has functions to directly access the method and path.  It'd certainly be good if the next version of Beast had a good way to parse the URL.

---

## Comment 22

> Username: DragonOsman  
> Created at: 2018-06-10 18:54:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-396072469  

Also, are the spaces in the request line still there, or are they gone by the time `handle_request` is called?  If I recall correctly, you told me before that they're taken out.

---

## Comment 23

> Username: vinniefalco  
> Created at: 2018-06-11 01:39:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-396100336  

> Also, are the spaces in the request line still there, or are they gone by the time `handle_request` is called?   
  
This is documented.  
  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__http__message/target/overload1.html  
  
And **request-target** is documented in the HTTP RFC:  
  
https://tools.ietf.org/html/rfc7230#section-3.1.1

---

## Comment 24

> Username: DragonOsman  
> Created at: 2018-06-11 17:11:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-396316594  

The functions `request::target` and `request::method` give you access to the target URI and the method in the request line, right?  So how come Beast isn't parsing the URI?  What do I need to do in `handle_request` that I'm not doing?    
  
I wonder if this code will help me:  
```cpp  
#include <boost/algorithm/string.hpp> // for case-insensitive string comparison  
...  
struct ParsedURI {  
  std::string protocol;  
  std::string domain;  // only domain must be present  
  std::string port;  
  std::string resource;  
  std::string query;   // everything after '?', possibly nothing  
};  
  
ParsedURI parseURI(const std::string& url) {  
  ParsedURI result;  
  auto value_or = [](const std::string& value, std::string&& deflt) -> std::string {  
    return (value.empty() ? deflt : value);  
  };  
  // Note: only "http", "https", "ws", and "wss" protocols are supported  
  static const std::regex PARSE_URL{ R"((([httpsw]{2,5})://)?([^/ :]+)(:(\d+))?(/([^ ?]+)?)?/?\??([^/ ]+\=[^/ ]+)?)",   
                                     std::regex_constants::ECMAScript | std::regex_constants::icase };  
  std::smatch match;  
  if (std::regex_match(url, match, PARSE_URL) && match.size() == 9) {  
    result.protocol = value_or(boost::algorithm::to_lower_copy(std::string(match[2])), "http");  
    result.domain   = match[3];    
    const bool is_sequre_protocol = (result.protocol == "https" || result.protocol == "wss");  
    result.port     = value_or(match[5], (is_sequre_protocol)? "443" : "80");  
    result.resource = value_or(match[6], "/");  
    result.query = match[8];  
    assert(!result.domain.empty());  
  }  
  return result;  
}  
```  
  
So I just need to parse the request URI in the request line?  But to get the domain, port and everything from the URL, I'll have to somehow get the URL the user typed in the browser to get to the application.  But I don't know how to access that.  I sent you an email asking about that (just about how to get the URL the user types in the browser, though I'd like to know the answer to the rest of it, too), so if this is too general of a question, please answer it by email.  Thanks in advance.

---

## Comment 25

> Username: vinniefalco  
> Created at: 2018-06-11 17:37:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-396324497  

Beast doesn't parse the URI, it just provides the string (and makes sure there are no illegal characters).  
  
> if this is too general of a question, please answer it by email  
  
Well, it doesn't matter whether the question comes in by email or by GitHub issue, I am unable to provide much assistance on general programming questions. You could try the C++ Slack (https://cpplang.now.sh), StackOverflow, or Reddit, to name a few places.

---

## Comment 26

> Username: DragonOsman  
> Created at: 2018-06-12 13:05:18 UTC  
> Updated at: 2018-06-12 13:05:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-396581955  

Does the Request class have a function that returns the query string (stuff after the '?' character, if there's anything there), like how there's a `Request::target` function and a `Request::method` function?  What header file should I look at for this?  I understand that the path would still be '/' and that I'd need to look for the query string `q=accesskey` in the request, so I'm asking if there's anything in Beast that I can compare the query string against like how I can check `req.method` to see if it's GET or POST.

---

## Comment 27

> Username: DragonOsman  
> Created at: 2018-06-12 14:21:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-396607162  

I printed `req.base` and got this:  
```  
Request::base is GET /?q=accesskey HTTP/1.1  
Referer: http://localhost:8080/  
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134  
Accept: text/plain  
Accept-Language: en-US  
Accept-Encoding: gzip, deflate  
Host: localhost:8080  
Connection: Keep-Alive  
```  
So the target is `/?q=accesskey` for the `localhost:8080/?q=accesskey` request.

---

## Comment 28

> Username: vinniefalco  
> Created at: 2018-06-12 14:51:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-396618123  

> Beast doesn't parse the URI, it just provides the string

---

## Comment 29

> Username: DragonOsman  
> Created at: 2018-06-13 20:34:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-397077243  

Could you tell me what I did wrong in `handle_request` when trying to send back the access key as the response body?  Here's the function, for reference:  
  
```cpp  
template<class Body, class Allocator, class Send>  
void handle_request(boost::beast::string_view doc_root, http::request<Body, http::basic_fields<Allocator>>&& req,  
	Send&& send, const char *accesskey/*, const char *apikey*/)  
{  
	// Returns a bad request response  
	const auto bad_request = [&req](boost::beast::string_view why)  
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
	const auto not_found = [&req](boost::beast::string_view target)  
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
	const auto server_error = [&req](boost::beast::string_view what)  
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
	std::string path;  
	if (req.target() != "/?q=accesskey")  
	{  
		path = path_cat(doc_root, req.target());  
		if (req.target().back() == '/')  
		{  
			path.append("index.html");  
		}  
	}  
  
	/*jinja2::Template tpl;  
	tpl.LoadFromFile(path.c_str());  
	jinja2::ValuesMap params = { { "apikey" }, std::string(apikey) };  
	std::string body_str = tpl.RenderToString(params);*/  
  
	// Attempt to open the file  
	boost::beast::error_code ec;  
	http::file_body::value_type body;  
	if (req.target() != "/?q=accesskey")  
	{  
		body.open(path.c_str(), boost::beast::file_mode::scan, ec);  
	}  
	//http::string_body::value_type str_body{ body_str };  
  
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
		if (req.target() == "/?q=accesskey")  
		{  
			std::cout << "Line 457: GET request for accesskey is: " << req << '\n';  
			http::string_body::value_type str_body_key{ accesskey };  
			std::cerr << "accesskey = " << accesskey << '\n';  
			http::response<http::string_body> res{  
				std::piecewise_construct,  
				std::make_tuple(std::move(str_body_key)),  
				std::make_tuple(http::status::ok, req.version()) };  
			res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
			res.set(http::field::content_type, "plain/text");  
			res.content_length(str_body_key.size());  
			res.keep_alive(req.keep_alive());  
			return send(std::move(res));  
		}  
		else  
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
	}  
  
	// Respond to POST request  
	else if (req.method() == http::verb::post)  
	{  
		boost::beast::string_view content_type = req[http::field::content_type];  
		if (content_type.find("multipart/form-data") == std::string::npos &&   
			content_type.find("application/x-www-form-urlencoded") == std::string::npos)  
		{  
			return send(bad_request("Bad request"));  
		}  
  
		std::map<std::string, std::string> parsed_value = parse(req.body());  
		std::cout << "POST body example:\n" << req.body() << '\n';  
		std::cout << "parsed_value map:\n";  
		for (const auto &x : parsed_value)  
		{  
			std::cout << x.first << ": " << x.second << '\n';  
		}  
		double money_amount = std::stod(parsed_value["currency_amount"]);  
		std::string to_currency = parsed_value["to_currency"];  
		std::cout << "Line 508: to_currency is: " << to_currency << '\n';  
		std::string currency_abbr = to_currency.substr(0, 3);  
		double conversion_result = convert(currency_abbr, money_amount, accesskey);  
		  
		http::string_body::value_type str_body_conv = std::to_string(conversion_result);  
		http::response<http::string_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(str_body_conv)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(str_body_conv.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
}  
```  
The part I need looked at is this one:  
```cpp  
if (req.target() == "/?q=accesskey")  
	{  
		std::cout << "Line 457: GET request for accesskey is: " << req << '\n';  
		http::string_body::value_type str_body_key{ accesskey };  
		std::cerr << "accesskey = " << accesskey << '\n';  
		http::response<http::string_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(str_body_key)),  
			std::make_tuple(http::status::ok, req.version()) };  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, "plain/text");  
		res.content_length(str_body_key.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
```  
What got sent to the client from here is:  
```  
{"notifyType":"consoleItemLog","message":{"message":"","styles":"","hasFormatString":true,"fileUrl":"http://localhost:8080/scripts/scripts-bundle.js","lineNumber":4118,"columnNumber":17}}  
```  
If this is too general or anything, then never mind; I'll ask somewhere else.

---

## Comment 30

> Username: DragonOsman  
> Created at: 2018-06-14 17:17:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-397371884  

I tried printing the response body that's created for the access key, and I saw in it the Content-Length header has a value of 0.  The `boost::beast::http::string_body::value_type` object I made and initialized with the value of the `const char*` object  `accesskey` is also blank (I printed it and saw that it has nothing in it).    
  
Here's the updated code: https://gist.github.com/DragonOsman/7bbdb049ea2a77173c5dafbecd5f1be2 .    
  
So what did I do wrong?  I'm guessing I need to understand more about the `string_body`.

---

## Comment 31

> Username: vinniefalco  
> Created at: 2018-06-14 17:54:38 UTC  
> Updated at: 2018-06-14 18:03:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-397383239  

Perhaps try following the scientific method? (Note: "Ask A Question" in this diagram does not mean create a new GitHub issue, it means to formulate a scientific query. For example "How should `handle_request` process a custom query string")  
  
![image](https://user-images.githubusercontent.com/1503976/41429184-34546f60-6fc1-11e8-8e9c-0052f00fc2b1.png)

---

## Comment 32

> Username: DragonOsman  
> Created at: 2018-06-14 23:05:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-397464501  

I'll probably address the query string problem later since it doesn't seem to be that pressing as of right now.  Right now I just want to know how to fix the problem with the response body being empty.    
  
For the query string, though, when I do address that: is it okay if I add a function to the library code to parse the URI to extract the stuff after `?` and propose it if I get it to a good level?

---

## Comment 33

> Username: vinniefalco  
> Created at: 2018-06-14 23:30:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-397468664  

> For the query string, though, when I do address that: is it okay if I add a function to the library code to parse the URI to extract the stuff after ?  
  
You're free to do as you wish with your own fork, that's the beauty of Git!  
  
> ...and propose it if I get it to a good level?  
  
Well...there's a pretty high bar. It would need to be significantly better than anything which currently exists. And it would have to fully implement everything described in https://tools.ietf.org/html/rfc3986

---

## Comment 34

> Username: DragonOsman  
> Created at: 2018-06-15 17:08:50 UTC  
> Updated at: 2018-06-15 17:09:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-397685087  

I've fixed the problem of getting the access key to the client and populating the "to" and "from" currency dropdowns.  It seems like I had to put `accesskey`, as a `std::string`, itself inside the `string_body` object directly rather than trying to initialize a separate `string_body::value_type` object.  I don't really understand why.    
  
Now there seems to be a Segmentation Fault in there.  It calls "abort()" at some point before making the currency conversion calculation or when it tries to send the result of the calculation to the client -- or at least that's what I'm thinking.  I'll try checking.  But it's not something Beast exclusive, so I won't ask about it here.

---

## Comment 35

> Username: DragonOsman  
> Created at: 2018-06-16 11:47:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-397807066  

I got the HTML template text to appear in the browser with the API key, but now the browser's console is saying that there's no function called `init_map` (that's the function I'm using as the callback in the URL for the script tag for linking in the Google JavaScript API), and the server app output has the message   
```  
Unexpected '}}'  
```  
The full error in the browser's console, if you want to see it, is:  
```  
SCRIPT5022: SCRIPT5022: InvalidValueError: init_map is not a function  
```  
What part of the server code could be giving the error "Unexpected '}}'"?  Is it inside the `for` loop in `main`?

---

## Comment 36

> Username: vinniefalco  
> Created at: 2018-06-16 11:54:06 UTC  
> Updated at: 2018-06-16 11:54:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-397807380  

> the browser's console is saying that there's no function called `init_map`  
  
You're asking me to help you debug Javascript?

---

## Comment 37

> Username: DragonOsman  
> Created at: 2018-06-16 13:44:08 UTC  
> Updated at: 2018-06-16 14:45:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-397813093  

Nope.  Just the reason for the `'}}' Unexpected` thing which is coming from the server code.  Of course I wouldn't ask you to help me debug JavaScript.  I was just saying that that's probably caused by that message coming from the server code.

---

## Comment 38

> Username: DragonOsman  
> Created at: 2018-06-16 14:45:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-397817010  

The updated C++ code is here: https://gist.github.com/DragonOsman/7bbdb049ea2a77173c5dafbecd5f1be2 .  I checked in the debugger; from how it gets stuck inside the `for` loop in `main` when I navigate to `localhost:8080` and the debugger goes unresponsive, I get the feeling the message comes from that loop.

---

## Comment 39

> Username: bjovke  
> Created at: 2018-06-20 14:38:18 UTC  
> Updated at: 2018-06-20 14:39:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-398773399  

@vinniefalco Hello. I'm not a frequent visitor of Beast GitHub repository and I accidentally stumbled upon this "Issue". First of all I have to say Vinnie that you are very active in helping people regarding the Beast library and I thank you for that although I haven't used Beast for any production project so far.  
  
@DragonOsman Osman, it really seems that your issue is not the issue with the Beast library itself, which is what this section of repository is for. Vinnie has already spent a lot of time helping you with this and you still haven't managed to make your program work. My recommendation for you is that you really need to analyze your code more thoroughly and come to the solution for your problem yourself, by using programming best practices.  
It's not fair that you have taken so much of Vinnie's free time. This kind of help you can usually get only with paid support contract and Vinnie has, in my opinion, gave you huge amount of time for free.  
This is also not fair to other contributors and people which have issues using Beast because a lot of time is spent on this "issue" without any real benefit for the library.

---

## Comment 40

> Username: DragonOsman  
> Created at: 2018-06-23 21:56:02 UTC  
> Updated at: 2018-06-23 21:56:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1150#issuecomment-399713224  

I'm sorry.  I didn't mean to take too much of his time.  The issue is already fixed, though.  Part of it in the middle was something with the library, but I was able to fix it on my own.
