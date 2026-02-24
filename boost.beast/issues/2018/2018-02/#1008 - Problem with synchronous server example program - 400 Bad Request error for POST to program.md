# #1008 - Problem with synchronous server example program - 400 Bad Request error for POST to program [Closed]

> Username: DragonOsman  
> Created at: 2018-02-06 19:49:47 UTC  
> Updated at: 2018-02-12 22:44:10 UTC  
> Closed at: 2018-02-12 22:44:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1008  

I'm trying to change the synchronous HTTP server example to work with POST requests and have it do what I need it to, but I'm having some problems.  The biggest one right now is what I've put up in the title of this issue thread.    
  
Here's my code:  
```cpp  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/config.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
#include "json.hpp"  
  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
using nlohmann::json;  
namespace http = boost::beast::http;    // from <boost/beast/http.hpp>  
  
										//------------------------------------------------------------------------------  
  
										// Return a reasonable mime type based on the extension of a file.  
boost::beast::string_view mime_type(boost::beast::string_view path);  
  
// This class represents a cache for storing results from the   
  
// currency exchange API used by openexchangerates.org  
  
class cache_storage  
{  
public:  
	cache_storage(const std::chrono::seconds &duration)  
		: m_cache{}, m_duration{ duration }, m_socket{ m_ioc }  
	{  
  
	}  
  
	// This function queries the currency API after making sure  
	// that the stored result(s) is/are old enough  
	// It also makes a new query to the API if needed  
	const json &query(const std::string &query_data);  
  
private:  
	std::unordered_map<std::string, std::pair<std::chrono::time_point<std::chrono::steady_clock>, json>> m_cache;  
	std::chrono::seconds m_duration;  
	boost::asio::io_context m_ioc;  
	tcp::socket m_socket;  
  
};  
  
// Append an HTTP rel-path to a local filesystem path.  
// The returned path is normalized for the platform.  
std::string path_cat(boost::beast::string_view base, boost::beast::string_view path);  
  
// This function produces an HTTP response for the given  
// request. The type of the response object depends on the  
// contents of the request, so the interface requires the  
// caller to pass a generic lambda for receiving the response.  
template<class Body, class Allocator, class Send>  
void handle_request(boost::beast::string_view doc_root, http::request<Body, http::basic_fields<Allocator>>&& req, Send&& send);  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void fail(boost::system::error_code ec, char const* what);  
  
// This is the C++11 equivalent of a generic lambda.  
// The function object is used to send an HTTP message.  
template<class Stream>  
struct send_lambda  
{  
	Stream& stream_;  
	bool& close_;  
	boost::system::error_code& ec_;  
  
	explicit  
		send_lambda(Stream& stream, bool& close, boost::system::error_code& ec)  
		: stream_{ stream }, close_{ close }, ec_{ ec }  
	{  
	}  
  
	template<bool isRequest, class Body, class Fields>  
	void operator()(http::message<isRequest, Body, Fields>&& msg) const;  
};  
  
// Handles an HTTP server connection  
void do_session(tcp::socket& socket, std::string const& doc_root);  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char* argv[])  
{  
	try  
	{  
		// Check command line arguments.  
		if (argc != 4)  
		{  
			std::cerr <<  
				"Usage: currency_converter <address> <port> <doc_root>\n" <<  
				"Example:\n" <<  
				"    currency_converter 0.0.0.0 8080 .\n";  
			return EXIT_FAILURE;  
		}  
		auto const address = boost::asio::ip::make_address(argv[1]);  
		auto const port = static_cast<unsigned short>(std::atoi(argv[2]));  
		std::string const doc_root = argv[3];  
  
		// The io_context is required for all I/O  
		boost::asio::io_context ioc{ 1 };  
  
		// The acceptor receives incoming connections  
		tcp::acceptor acceptor{ ioc, { address, port } };  
		for (;;)  
		{  
			// This will receive the new connection  
			tcp::socket socket{ ioc };  
  
			// Block until we get a connection  
			acceptor.accept(socket);  
  
			// Launch the session, transferring ownership of the socket  
			std::thread{ std::bind(&do_session, std::move(socket), doc_root) }.detach();  
		}  
	}  
	catch (const std::exception& e)  
	{  
		std::cerr << "Error: " << e.what() << std::endl;  
		return EXIT_FAILURE;  
	}  
}  
  
boost::beast::string_view mime_type(boost::beast::string_view path)  
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
  
std::string path_cat(boost::beast::string_view base, boost::beast::string_view path)  
{  
	if (base.empty())  
	{  
		return path.to_string();  
	}  
	std::string result = base.to_string();  
#if BOOST_MSVC  
	char constexpr path_separator = '\\';  
	if (result.back() == path_separator)  
	{  
		result.resize(result.size() - 1);  
	}  
	result.append(path.data(), path.size());  
	for (auto& c : result)  
	{  
		if (c == '/')  
		{  
			c = path_separator;  
		}  
	}  
#else  
	char constexpr path_separator = '/';  
	if (result.back() == path_separator)  
	{  
		result.resize(result.size() - 1);  
	}  
	result.append(path.data(), path.size());  
#endif  
	return result;  
}  
  
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
			auto pos = path.rfind(".");  
			if (pos == boost::beast::string_view::npos)  
			{  
				return boost::beast::string_view{};  
			}  
			pos = path.find(" ");  
			if (pos != boost::beast::string_view::npos)  
			{  
				return boost::beast::string_view{};  
			}  
			auto pos2 = path.find(".");  
			return boost::beast::string_view(path.substr(pos2));  
		}();  
		if (!iequals(ext, ".exe"))  
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
  
		std::cout << "The POST body in handle_request():\n" << req.body() << '\n';  
		json j_res = json::parse(req.body());  
  
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
  
void fail(boost::system::error_code ec, char const* what)  
{  
	std::cerr << what << ": " << ec.message() << "\n";  
}  
  
template<class Stream>  
template<bool isRequest, class Body, class Fields>  
void send_lambda<Stream>::operator()(http::message<isRequest, Body, Fields>&& msg) const  
{  
	// Determine if we should close the connection after  
	close_ = msg.need_eof();  
  
	// We need the serializer here because the serializer requires  
	// a non-const file_body, and the message oriented version of  
	// http::write only works with const messages.  
	http::serializer<isRequest, Body, Fields> sr{ msg };  
	http::write(stream_, sr, ec_);  
}  
  
void do_session(tcp::socket& socket, std::string const& doc_root)  
{  
	bool close = false;  
	boost::system::error_code ec;  
  
	// This buffer is required to persist across reads  
	boost::beast::flat_buffer buffer;  
  
	// This lambda is used to send messages  
	send_lambda<tcp::socket> lambda{ socket, close, ec };  
  
	for (;;)  
	{  
		// Read a request  
		http::request<http::string_body> req;  
		http::read(socket, buffer, req, ec);  
		if (ec == http::error::end_of_stream)  
		{  
			break;  
		}  
		if (ec)  
		{  
			return fail(ec, "read");  
		}  
  
		// Send the response  
		handle_request(doc_root, std::move(req), lambda);  
		if (ec)  
		{  
			return fail(ec, "write");  
		}  
		if (close)  
		{  
			// This means we should close the connection, usually because  
			// the response indicated the "Connection: close" semantic.  
			break;  
		}  
	}  
  
	// Send a TCP shutdown  
	socket.shutdown(tcp::socket::shutdown_send, ec);  
  
	// At this point the connection is closed gracefully  
}  
  
const json &cache_storage::query(const std::string &query_data)  
{  
	auto found = m_cache.find(query_data);  
	if (found == m_cache.end() || (std::chrono::steady_clock::now() - found->second.first) > m_duration)  
	{  
		std::string host{ "https://openexchangerates.org/" }, api_endpoint{ "/api/latest.json" },  
			app_id{ "APP_ID" }, symbol{ query_data }, prettyprint{ "true" }, show_alternative{ "false" },  
			target = api_endpoint + "?app_id=" + app_id + "&symbols=" + symbol + "&prettyprint=" + prettyprint + "&show_alternative=" + show_alternative,  
			port{ "443" };  
		int version = 11;  
  
		tcp::resolver resolver{ m_ioc };  
		const auto results = resolver.resolve(host, port);  
		boost::asio::connect(m_socket, results.begin(), results.end());  
  
		http::response<http::string_body> res;  
  
		// Set up an HTTP GET request message  
		http::request<http::string_body> req{ http::verb::get, target, version };  
		req.set(http::field::host, host);  
		req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
		// Send the HTTP request to the remote host  
		http::write(m_socket, req);  
  
		boost::beast::flat_buffer buffer;  
		http::read(m_socket, buffer, res);  
		found = m_cache.insert_or_assign(found, query_data, std::make_pair(std::chrono::steady_clock::now(), json::parse(res.body())));  
	}  
	return found->second.second;  
}  
```  
The problem is here:  
```cpp  
else if (req.target().front() == '/' && req.target().size() > 1 && req.method() == http::verb::post)  
{  
	using boost::beast::iequals;  
	const auto ext = [&path]  
	{  
		auto pos = path.rfind(".");  
		if (pos == boost::beast::string_view::npos)  
		{  
			return boost::beast::string_view{};  
		}  
		pos = path.find(" ");  
                if (pos != boost::beast::string_view::npos)  
		{  
			return boost::beast::string_view{};  
		}  
		auto pos2 = path.find(".");  
		return boost::beast::string_view(path.substr(pos2));  
	}();  
	if (!iequals(ext, ".exe"))  
	{  
		return send(bad_request("Illegal request-target"));  
	}  
}  
```  
Any help would be appreciated.  Thanks.

---

## Comment 1

> Username: DragonOsman  
> Created at: 2018-02-10 23:55:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1008#issuecomment-364706129  

Turns out it was just a problem in the action attribute of the HTML form on the client side.  Sorry about the false alarm.    
  
By the way.  In here:  
```cpp  
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
		double money_amount = std::stod(parsed_value["currency_amount"]);  
		std::string to_currency = parsed_value["to_currency"];  
		double conversion_result = convert(to_currency, money_amount);  
  
		http::response<http::file_body> res{  
			std::piecewise_construct,  
			std::make_tuple(std::move(body)),  
			std::make_tuple(http::status::ok, req.version()) };  
		body >> conversion_result;  
		res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
		res.set(http::field::content_type, mime_type(path));  
		res.content_length(body.size());  
		res.keep_alive(req.keep_alive());  
		return send(std::move(res));  
	}  
```  
Would `body >> conversion_result` be good for putting the result inside the response body, or do I have to do something else for that (if I should open a new issue for this, let me know)?

---

## Comment 2

> Username: DragonOsman  
> Created at: 2018-02-12 22:44:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1008#issuecomment-365088797  

This was resolved as well.  I'm closing this now because there's no more use in keeping it open.
