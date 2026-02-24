# #1274 - Async version of app code (is this good?) [Closed]

> Username: DragonOsman  
> Created at: 2018-10-21 16:01:53 UTC  
> Updated at: 2018-11-28 17:19:33 UTC  
> Closed at: 2018-11-28 17:19:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1274  

I wanted to make the server code for my app asynchronous so that I could try making a Nodejs app of it.  I know ASIO doesn't support WebAssembly, but wanted to know if there was at least a workaround for this.  If there is one but it's too hard, then I'll make it a Nodejs app but won't also try to port it to WebAssembly.  I still want an async version of the code so it'll be easier to run it through Nodejs.    
  
I've tried converting the server code so far.  If I also need to convert the client code, let me know.  Anyways, I've put the changed code up on Gist.  Here's the link: https://gist.github.com/DragonOsman/d00ea7ec3d49c128ad7789293f156a09 .  Please let me know if I've made any mistakes.  The `apikey` and `accesskey` need to be given to the app as environment variables so that it'll work correctly.  I've given them as arguments to `session's constructor.  Is that a good idea?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-21 16:32:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1274#issuecomment-431683246  

I have no idea about any of that, sorry

---

## Comment 2

> Username: DragonOsman  
> Created at: 2018-10-21 20:03:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1274#issuecomment-431699376  

Well, right now I just need to know how to convert it from sync to async.  I tried compiling the version I just came up with but it had errors.    
  
The link for the code is the same as in the OP; I just updated it.  The errors I got were:  
```  
currency_converter.cpp:178:75: error: no matching function for call to ‘handle_request(std::__shared_ptr_access<const std::__cxx11::basic_string<char>, (__gnu_cxx::_Lock_policy)2, false, false>::element_type&, std::remove_reference<boost::beast::http::message<true, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >&>::type, session::send_lambda&, const char&, const char&)’  
   handle_request(*doc_root_, std::move(req_), lambda_, *accesskey, *apikey);  
                                                                           ^  
currency_converter.cpp:83:6: note: candidate: template<class Body, class Allocator, class Send> void handle_request(boost::beast::string_view, boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >&&, Send&&, const char*, const char*)  
 void handle_request(boost::beast::string_view doc_root, http::request<Body, http::basic_fields<Allocator>> &&req,  
      ^~~~~~~~~~~~~~  
currency_converter.cpp:83:6: note:   template argument deduction/substitution failed:  
currency_converter.cpp:178:56: note:   cannot convert ‘*((session*)this)->session::accesskey’ (type ‘const char’) to type ‘const char*’  
   handle_request(*doc_root_, std::move(req_), lambda_, *accesskey, *apikey);  
                                                        ^~~~~~~~~~  
currency_converter.cpp: In member function ‘const json& cache_storage::query(const std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >&, const char*)’:  
currency_converter.cpp:816:17: error: ‘connect’ is not a member of ‘boost::asio’  
    boost::asio::connect(socket, results.begin(), results.end());  
                 ^~~~~~~  
currency_converter.cpp:816:17: note: suggested alternatives:  
In file included from /usr/include/netinet/in.h:23:0,  
                 from /usr/include/netdb.h:27,  
                 from /usr/local/boost_1_68_0/boost/asio/error.hpp:28,  
                 from /usr/local/boost_1_68_0/boost/asio/detail/impl/posix_mutex.ipp:24,  
                 from /usr/local/boost_1_68_0/boost/asio/detail/posix_mutex.hpp:73,  
                 from /usr/local/boost_1_68_0/boost/asio/detail/mutex.hpp:25,  
                 from /usr/local/boost_1_68_0/boost/asio/detail/service_registry.hpp:20,  
                 from /usr/local/boost_1_68_0/boost/asio/impl/execution_context.hpp:20,  
                 from /usr/local/boost_1_68_0/boost/asio/execution_context.hpp:408,  
                 from /usr/local/boost_1_68_0/boost/asio/detail/scheduler.hpp:21,  
                 from /usr/local/boost_1_68_0/boost/asio/system_context.hpp:19,  
                 from /usr/local/boost_1_68_0/boost/asio/impl/system_executor.hpp:22,  
                 from /usr/local/boost_1_68_0/boost/asio/system_executor.hpp:129,  
                 from /usr/local/boost_1_68_0/boost/asio/associated_executor.hpp:21,  
                 from /usr/local/boost_1_68_0/boost/beast/core/detail/bind_handler.hpp:15,  
                 from /usr/local/boost_1_68_0/boost/beast/core/bind_handler.hpp:15,  
                 from /usr/local/boost_1_68_0/boost/beast/core.hpp:15,  
                 from currency_converter.cpp:17:  
/usr/include/x86_64-linux-gnu/sys/socket.h:126:12: note:   ‘connect’  
 extern int connect (int __fd, __CONST_SOCKADDR_ARG __addr, socklen_t __len);  
            ^~~~~~~  
In file included from /usr/local/boost_1_68_0/boost/asio/detail/socket_ops.hpp:336:0,  
                 from /usr/local/boost_1_68_0/boost/asio/detail/socket_holder.hpp:20,  
                 from /usr/local/boost_1_68_0/boost/asio/detail/reactive_socket_accept_op.hpp:24,  
                 from /usr/local/boost_1_68_0/boost/asio/detail/reactive_socket_service.hpp:30,  
                 from /usr/local/boost_1_68_0/boost/asio/basic_socket.hpp:40,  
                 from /usr/local/boost_1_68_0/boost/asio/basic_socket_acceptor.hpp:20,  
                 from /usr/local/boost_1_68_0/boost/asio/ip/tcp.hpp:19,  
                 from currency_converter.cpp:20:  
/usr/local/boost_1_68_0/boost/asio/detail/impl/socket_ops.ipp:473:5: note:   ‘boost::asio::detail::socket_ops::connect’  
 int connect(socket_type s, const socket_addr_type* addr,  
     ^~~~~~~  
```  
I have it on a Linux VM as well, and this is a build attempt from there.    
  
Anyway, yeah, could someone help me with this?

---

## Comment 3

> Username: DragonOsman  
> Created at: 2018-11-01 22:37:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1274#issuecomment-435210595  

@djarek So if it's hard to port code using Asio to WebAssembly since Asio doesn't support it, it should at least be possible, shouldn't it?  I can either try this or just turn my C++ code into an Addon for Node.js if that'll be easier (I just don't know where to look to figure out if I can easily port my code or not).  I'm talking about using Google's Node.js V8 engine, the same engine that powers Node.js and is written in C++.  I could try to learn to use that.  What do you think?

---

## Comment 4

> Username: djarek  
> Created at: 2018-11-01 22:53:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1274#issuecomment-435213839  

In theory, it should be possible to port ASIO over to WebAssembly and use Node's APIs as the backend for ASIO's implementation. However, that might prove to be very difficult and I think using Node's APIs directly is the better choice, unless you have an extensive codebase and porting your code requires more effort than porting ASIO.

---

## Comment 5

> Username: DragonOsman  
> Created at: 2018-11-03 11:27:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1274#issuecomment-435580500  

I heard that I'll have to understand fully how Node works before I can expose my C++ app's networking functionality to it.  So it might actually be pretty complicated.  Unless I can expose just the `convert` function--but then wouldn't I also need the `cache_storage::query` function, which is making a GET request to the currency API, anyway?    
  
My code is here: https://github.com/DragonOsman/currency_converter .  Could you let me know if it'll be harder to port my code to Node's APIs?

---

## Comment 6

> Username: djarek  
> Created at: 2018-11-03 20:21:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1274#issuecomment-435617915  

@DragonOsman I'd like to help, but I don't know enough about Node's networking APIs in order to properly form an informed opinion and help you. Sorry!

---

## Comment 7

> Username: DragonOsman  
> Created at: 2018-11-03 20:36:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1274#issuecomment-435618921  

Never mind, then.  I'll try to ask someone at the Node developer team when I get to it.

---

## Comment 8

> Username: DragonOsman  
> Created at: 2018-11-09 10:52:38 UTC  
> Updated at: 2018-11-09 11:50:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1274#issuecomment-437324079  

I'm going to ask for help here on updating my code to have it use asynchronous networking functions.  I want to call `std::thread` directly like I already am instead of indirectly via `std::bind`, though, so please help me out with that too (last time I tried it, I had errors because of the port, address and socket objects).    
  
Do I need to change it entirely to use the asynchronous HTTP server and client examples, or is there a way to do this without having to do that?  
  
@vinniefalco I think you can help me with this?  Not asking about the Node or WebAssembly stuff, just making my code async.

---

## Comment 9

> Username: DragonOsman  
> Created at: 2018-11-11 23:43:47 UTC  
> Updated at: 2018-11-11 23:58:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1274#issuecomment-437716661  

I just changed the server code in my C++ app to the async version.  I need help on the client code, though.  I've uploaded the .cpp file my `currency_converter` repository on GitHub; [here](https://github.com/DragonOsman/currency_converter/blob/master/currency_converter_async.cpp)'s the link to the .cpp file itself.    
  
My function where I make the client request to the currency API for the conversion rates looks like this:  
```cpp  
// This function queries the currency API after making sure  
// that the stored result(s) is/are old enough  
// It also makes a new query to the API if needed  
const json &cache_storage::query(const std::map<std::string, std::string> &query_data, const char *accesskey, const json &sentry)  
{  
	auto found = m_cache.find(query_data);  
	boost::beast::error_code ec;  
	try  
	{  
		if (found == m_cache.end() || (std::chrono::steady_clock::now() - found->second.first) > m_duration)  
		{  
			std::string host{ "apilayer.net" }, api_endpoint{ "/api/live" },  
				key{ accesskey }, source{ query_data.at(std::string("from_currency")) }, currency_param{ query_data.at(std::string("to_currency")) };  
			std::string target;  
			if (query_data.at(std::string("from_currency")) != "USD")  
			{  
				target = api_endpoint + "?access_key=" + accesskey + "&source=" + source + "&currencies=" + currency_param + "&format=1";  
			}  
			else  
			{  
				target = api_endpoint + "?access_key=" + accesskey + "&currencies=" + currency_param + "&format=1";  
			}  
			std::string port{ "80" };  
			int version = 11;  
  
			// The io_context is required for all IO  
			boost::asio::io_context ioc;  
  
			// These objects perform our IO  
			tcp::resolver resolver{ ioc };  
			tcp::socket socket{ ioc };  
  
			// Look up the domain name  
			const auto results = resolver.resolve(host, port);  
  
			// Make the connection on the IP address we get from a lookup  
			boost::asio::connect(socket, results.begin(), results.end());  
  
			// Set up an HTTP GET request message  
			http::request<http::string_body> req{ http::verb::get, target, version };  
			req.set(http::field::host, host);  
			req.set(http::field::content_type, "application/json");  
			req.set(http::field::accept, "application/json");  
			req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
			// Send the HTTP request to the remote host  
			http::write(socket, req);  
  
			// This buffer is used for reading and must be persisted  
			boost::beast::flat_buffer buffer;  
  
			// Declare a container to hold the response  
			http::response<http::string_body> res;  
  
			// Receive the HTTP response  
			http::read(socket, buffer, res, ec);  
			found = m_cache.insert_or_assign(found, query_data, std::make_pair(std::chrono::steady_clock::now(), json::parse(res.body())));  
		}  
		return found->second.second;  
	}  
	catch (const std::exception &e)  
	{  
		std::cerr << "Line 828: Error: " << e.what() << '\n';  
	}  
	catch (const boost::beast::error_code &ec)  
	{  
		std::cerr << "Line 832: Error: " << ec.message() << '\n';  
	}  
	return sentry;  
}  
```  
I need to know how to convert this to the async client version correctly.  I noticed that the request and response stuff is done in the `session` class, so I want to ask: will it be okay to put the code for checking whether the query I want to make has already been made, and to make a new one if it's not inside the `map` or if the one in there is an hour old, inside the `session` class somewhere?  What should I for this?  I'm thinking that the client code may also need to be async for the app to work better as a C++ Addon for Node.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-11-11 23:57:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1274#issuecomment-437717777  

Hmm...you're supposed to already know how to use Asio, especially the asynchronous parts. This is stated in the documentation:  
  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/introduction.html#beast.introduction.requirements  
  
You would probably make faster overall progress if you paused your work on this beast program, and instead focus your energies on learning Asio's asynchronous interfaces. Then once you have mastered that, you should be able to answer the questions you have asked here, and you can apply that knowledge to your beast programs.
