# #1522 - Problem with sending encrypted GET request [Closed]

> Username: DragonOsman  
> Created at: 2019-03-16 23:20:03 UTC  
> Updated at: 2019-03-17 00:51:49 UTC  
> Closed at: 2019-03-17 00:51:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1522  

### Version of Beast  
Version 189  
  
### Steps necessary to reproduce the problem  
  
Making a GET request to the bankersalgo.com's currency API for a list of currencies with the following code:  
```cpp  
/ This function queries the currency API for a list of currencies  
const json &cache_storage::query_list(const char *currencykey, const json &sentry)  
{  
	boost::beast::error_code ec;  
	try  
	{  
		std::string host{ "bankersalgo.com" }, api_endpoint{ "/currencies_list" }, key{ currencykey };  
		std::string target = api_endpoint + '/' + key;  
		std::string port{ "443" };  
		int version = 11;  
  
		// The io_context is required for all IO  
		boost::asio::io_context ioc;  
  
		// The SSL context is required, and holds certificates  
		ssl::context ctx{ ssl::context::tlsv12_client };  
  
		// This holds the root certificate used for verification  
		load_root_certificates(ctx);  
  
		// Verify the remote server's certificate  
		ctx.set_verify_mode(ssl::verify_peer);  
  
		// These objects perform our IO  
		tcp::resolver resolver{ ioc };  
		ssl::stream<tcp::socket> stream{ ioc, ctx };  
  
		// Set SNI Hostname (many hosts need this to handshake successfully)  
		if (!SSL_set_tlsext_host_name(stream.native_handle(), host.c_str()))  
		{  
			boost::system::error_code ec{ static_cast<int>(::ERR_get_error()), boost::asio::error::get_ssl_category() };  
			throw boost::system::system_error{ ec };  
		}  
  
		// Look up the domain name  
		const auto results = resolver.resolve(host, port);  
  
		// Make the connection on the IP address we get from a lookup  
		boost::asio::connect(stream.next_layer(), results.begin(), results.end());  
  
		// Set up an HTTP GET request message  
		http::request<http::string_body> req{ http::verb::get, target, version };  
		req.set(http::field::host, host);  
		req.set(http::field::content_type, "application/json");  
		req.set(http::field::accept, "application/json");  
		req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
		req.set(http::field::content_encoding, "gzip");  
  
		// Send the HTTP request to the remote host  
		http::write(stream, req);  
  
		// This buffer is used for reading and must be persisted  
		boost::beast::flat_buffer buffer;  
  
		// Declare a container to hold the response  
		http::response<http::string_body> res;  
  
		// Receive the HTTP response  
		http::read(stream, buffer, res);  
		std::cout << res.body() << "\n";  
  
		// Gracefully close the stream  
		boost::system::error_code ec;  
		stream.shutdown(ec);  
		if (ec == boost::asio::error::eof)  
		{  
			// Rationale:  
			// http://stackoverflow.com/questions/25587403/boost-asio-ssl-async-shutdown-always-finishes-with-an-error  
			ec.assign(0, ec.category());  
		}  
		if (ec)  
		{  
			throw boost::system::system_error{ ec };  
		}  
  
		// If we get here then the connection is closed gracefully  
  
		return json::parse(res.body());  
	}  
	catch (const boost::beast::error_code &ec)  
	{  
		std::cerr << "Line 862: Error: " << ec.message() << '\n';  
	}  
	catch (const json::exception &e)  
	{  
		std::cerr << "Line 866: Error: " << e.what() << '\n';  
	}  
	catch (const std::exception &e)  
	{  
		std::cerr << "Line 870: Error: " << e.what() << '\n';  
	}  
	return sentry;  
}  
```  
The full app source code can be found on GitHub [here](https://github.com/DragonOsman/currency_converter).  
  
When I navigate to the app in the browser when the server app is running, the server app crashes with this output after the Google Map pans to my location and opens the info window:  
```  
Line 870: Error: uninitialized  
  
E:\programming\visual_studio_2017\Projects\currency_converter\x64\Release\currency_converter.exe (process 11928) exited with code -1073740791.  
Press any key to close this window . . .  
```  
Should I send an email to the Boost users mailing list and also to the author of the Asio library, too?  Since this is a problem with Asio as well.  And I'm sorry for the repeated emails if it's considered a problem.    
  
I did try to narrow it down as much as I could this time, though.  And as for the root certificates, I just loaded all of the certificates from the Windows root certificate store and sent them to the buffer passed to `add_certificate_authority()` one by one in the loop I extracted the certificates from the store in.    
  
Anyway, I tried printing the type of the exception I caught and found that it's a `struct boost::wrapexcept<class boost::system::system_error>` exception.
