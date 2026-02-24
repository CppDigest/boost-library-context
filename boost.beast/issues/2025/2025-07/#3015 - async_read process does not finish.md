# #3015 - async_read process does not finish [Closed]

> Username: albxy  
> Created at: 2025-07-01 12:42:46 UTC  
> Updated at: 2025-07-01 13:39:44 UTC  
> Closed at: 2025-07-01 13:39:44 UTC  
> Url: https://github.com/boostorg/beast/issues/3015  

` class session : public std::enable_shared_from_this<session>  
 {  
     ssl::stream<beast::tcp_stream> stream_;  
     beast::flat_buffer buffer_;  
     config& cfg_;  
     boost::optional<http::request_parser<http::empty_body>> parser_;  
     logic::request_information req_info;  
     void (*request_handler_)(logic::request_information& req_info, ssl::stream<beast::tcp_stream>& stream, beast::flat_buffer& buffer, config& cfg, std::shared_ptr<session> session_ptr) = nullptr;  
 public:  
     explicit  
         session(  
             tcp::socket&& socket,  
             ssl::context& ctx,  
             config &cfg,  
             void (*request_handler)(logic::request_information& req_info, ssl::stream<beast::tcp_stream>& stream, beast::flat_buffer& buffer, config& cfg, std::shared_ptr<session> session_ptr))  
         : stream_(std::move(socket), ctx)  
         , cfg_(cfg)  
         , request_handler_(request_handler)  
     {  
     }  
  
     ~session()  
     {  
            std::cout << "session destructor called\n";  
     }  
      
     void run()  
     {  
         net::dispatch(  
             stream_.get_executor(),  
             beast::bind_front_handler(  
                 &session::on_run,  
                 shared_from_this()));  
     }  
  
     void on_run()  
     {  
          
         beast::get_lowest_layer(stream_).expires_after(  
             std::chrono::seconds(3600));  
  
           
         stream_.async_handshake(  
             ssl::stream_base::server,  
             beast::bind_front_handler(  
                 &session::on_handshake,  
                 shared_from_this()));  
     }  
  
     void on_handshake(beast::error_code ec)  
     {  
         if (ec)  
             return fail(ec, "handshake");  
  
         do_read();  
     }  
  
     void do_read()  
     {  
  
         // Set the timeout.  
         beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(3600));  
  
         parser_.emplace();  
         // Read a request  
         http::async_read_header(stream_, buffer_, *parser_,  
             beast::bind_front_handler(  
                 &session::on_read,  
                 shared_from_this()));  
     }  
  
     void on_read(  
         beast::error_code ec,  
         std::size_t bytes_transferred)  
     {  
         boost::ignore_unused(bytes_transferred);  
  
         // This means they closed the connection  
         if (ec == http::error::end_of_stream)  
             return do_close();  
  
         if (ec)  
             return fail(ec, "on read");  
         auto result = boost::urls::parse_origin_form(parser_->get().target());  
         if (!result) {  
             //std::cerr << "Error parsing URI: " << result.error().message() << std::endl;  
  
             return fail(result.error(), "parse uri");  
         }  
         req_info.method = parser_->get().method_string();  
         req_info.decoded_req_target = result->path();  
         std::cout << "Before get info\n";  
         logic::get_request_information(parser_->get(), req_info);  
std::cout << "After get info\n";  
request_handler_(req_info, stream_, buffer_, cfg_, shared_from_this());  
         std::cout << "After handler\n";  
     }`  
### Above is part of the class "session"  
### Below is the function "handler_ " in the global scope  
`void handler_(logic::request_information& req_info, ssl::stream<beast::tcp_stream>& stream, beast::flat_buffer& buffer, server::config& cfg, std::shared_ptr<server::session> session_ptr)  
{  
	http::request<http::string_body> req;  
    http::async_read(stream, buffer, req,  
        [req_info, &stream, &cfg, session_ptr, req](beast::error_code ec, std::size_t bytes_transferred)  
        {  
			std::cout << "In async_read callback\n";  
            if (ec)  
            {  
                fail(ec,"read");  
                return;  
            }  
  
            http::response <http::string_body> res;  
			res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
			res.set(http::field::content_type, "text/html");  
			res.keep_alive(req.keep_alive());  
			res.body() = "Hello, World!"; // Placeholder response body  
			res.prepare_payload();  
            
            // Write the response  
            http::async_write(stream, res, [&session_ptr](beast::error_code ec, std::size_t bytes_transferred) {  
				std::cout << "In async_write callback\n";  
				session_ptr->on_write(1,ec, bytes_transferred);  
                });  
		});  
}`  
### The async_read function in "handler_" keeps running but does not call the callback lambada after receiving a request(the request is very small).When the client cut the connection directly,the callback function was called,and ec represents something like "Terminate network connection by local system"

---

## Comment 1

> Username: albxy  
> Created at: 2025-07-01 13:39:44 UTC  
> Url: https://github.com/boostorg/beast/issues/3015#issuecomment-3024073176  

forget to put the formal parser to init the present parser.
