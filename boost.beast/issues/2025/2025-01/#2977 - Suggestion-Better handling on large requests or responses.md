# #2977 - Suggestion:Better handling on large requests or responses [Closed]

> Username: albxy  
> Created at: 2025-01-29 14:31:31 UTC  
> Updated at: 2025-12-13 14:53:00 UTC  
> Closed at: 2025-02-01 03:50:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2977  

I have met a "read:body limit exceeded" while using the http(s) examples on boost.org.After searching for a while,I have found out that many people have met this problem.Although it seems it can be solved by using ..._parser,but it cannot fit the examples well,and seems a bit complexed(no complete solutions&I am a bit lazy...).Maybe we can just update the bodies(like providing a way to set body_limit as parsers do).This could be a total fix about this kind of issue.  
By the way,if anyone has a temporary complete solution,please tell me.

---

## Comment 1

> Username: albxy  
> Created at: 2025-01-30 08:01:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2977#issuecomment-2623787779  

Happily,I have found a temp solution:that is to change the basic_parser.hpp.Find the default_body_limit() function,and change the value 1024*1024 or 8*1024*1024 depending on your likes.This can solve the issue(though I still want a function to change the value at any time instead of only compiling)

---

## Comment 2

> Username: sehe  
> Created at: 2025-02-01 01:19:52 UTC  
> Updated at: 2025-02-01 01:20:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2977#issuecomment-2628658133  

@albxy It would be pretty trivial to write your own wrapper function instead. Here's an example. For fun, I named it `async_read` so you can see how it can seemlessly be your own extra overload. In production code I'd probably name it `async_read_with_limit` instead:  
  
```c++  
template <typename AsyncWriteStream, typename DynamicBuffer, bool IsRequest, typename Body,  
          typename Token = net::deferred_t>  
auto async_read(AsyncWriteStream& s, DynamicBuffer& b, size_t body_limit, http::message<IsRequest, Body>& m,  
                Token&& token = {}) //  
{  
    struct State {  
        AsyncWriteStream& s;  
        DynamicBuffer&    b;  
        size_t            body_limit;  
  
        http::message<IsRequest, Body>& m;  
        std::conditional_t<IsRequest,                  //  
                           http::request_parser<Body>, //  
                           http::response_parser<Body>>  
             p{};  
        bool init = true;  
  
        State(AsyncWriteStream& s, DynamicBuffer& b, size_t body_limit, http::message<IsRequest, Body>& m)  
            : s(s), b(b), body_limit(body_limit), m(m) {}  
    };  
    return net::async_compose<Token, void(beast::error_code, std::size_t)>(  
        [op_ = std::make_unique<State>(s, b, body_limit, m)] //  
        (auto& self, beast::error_code ec = {}, std::size_t bytes_transferred = 0) mutable {  
            if (auto& op = *op_; std::exchange(op.init, false)) {  
                op.p.body_limit(op.body_limit);  
                http::async_read(op.s, op.b, op.p, std::move(self));  
            } else {  
                op.m = op.p.release();  
                op_.reset(); // discard state before completion  
                self.complete(ec, bytes_transferred);  
            }  
        },  
        token);  
}  
```  
  
Most of the complexity is just the genericity. If you don't require it, it could look like  
  
```c++  
template <typename Token = net::deferred_t>  
auto async_read_with_limit(tcp::socket& s, beast::flat_buffer& b, size_t body_limit,  
                 http::response<http::string_body>& m, Token&& token = {}) {  
    return net::async_compose<Token, void(beast::error_code, std::size_t)>(  
        [&s, &b, body_limit, &m, init = true,  
         pp = std::make_unique<http::response_parser<http::string_body>>()](  
            auto& self, beast::error_code ec = {}, std::size_t bytes_transferred = 0) mutable {  
            if (auto& p = *pp; std::exchange(init, false)) {  
                p.body_limit(body_limit);  
                http::async_read(s, b, p, std::move(self));  
            } else {  
                m = p.release();  
                pp.reset(); // discard state before completion  
                self.complete(ec, bytes_transferred);  
            }  
        },  
        token);  
}  
```  
  
You can test it with a simple test program like this: https://coliru.stacked-crooked.com/a/b9c1f588b058241c  
  
```bash  
python -m http.server 8989 --bind 127.0.0.1&  
g++ -std=c++23 -O2 -pedantic -pthread main.cpp && ./a.out  
```  
  
https://github.com/user-attachments/assets/ad32a318-0740-460e-aa99-07f1edf97ad4

---

## Comment 3

> Username: albxy  
> Created at: 2025-02-01 03:50:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2977#issuecomment-2628748384  

@sehe Thanks a lot!The codes do fit seemlessly!However,I found a new problem:if the request is too large,the RAM exploded...So now I am figuring out how to use file_body to handle large file uploads.Anyway,this issue can be closed now.

---

## Comment 4

> Username: albxy  
> Created at: 2025-02-03 09:09:55 UTC  
> Updated at: 2025-12-13 14:53:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2977#issuecomment-2630361428  

@sehe The function does not work very well with file_body.It outputs"read:bad file descriptor".I found no way to deal with it...(Also the file does not release well if we call do_read() in function on_write(),it will output"another program is using...")But the following code works well:`// Handles an HTTP server connection  
class session : public std::enable_shared_from_this<session>  
{  
    ssl::stream<beast::tcp_stream> stream_;  
    beast::flat_buffer buffer_;  
    std::shared_ptr<std::string const> doc_root_;  
    http::request<http::file_body> req_;  
    net::steady_timer total_connection_timer_; // Timer for total connection duration  
  
public:  
    // Take ownership of the socket  
    explicit  
        session(  
            tcp::socket&& socket,  
            ssl::context& ctx,  
            std::shared_ptr<std::string const> const& doc_root)  
        : stream_(std::move(socket), ctx)  
        , doc_root_(doc_root)  
        , total_connection_timer_(stream_.get_executor())  
    { }  
  
    // Start the asynchronous operation  
    void  
        run()  
    {  
        // Start the connection timer when the first request is received  
        total_connection_timer_.expires_after(std::chrono::seconds(3600 * 24));  // Set to desired connection timeout (e.g., 10 seconds)  
  
        net::dispatch(  
            stream_.get_executor(),  
            beast::bind_front_handler(  
                &session::on_run,  
                shared_from_this()));  
    }  
  
    void  
        on_run()  
    {  
        // Perform the SSL handshake  
        stream_.async_handshake(  
            ssl::stream_base::server,  
            beast::bind_front_handler(  
                &session::on_handshake,  
                shared_from_this()));  
    }  
  
    void  
        on_handshake(beast::error_code ec)  
    {  
        if (ec)  
            return fail(ec, "handshake");  
  
        do_read();  
    }  
  
    void  
        do_read()  
    {  
        beast::error_code file_ec;  
        // Make the request empty before reading,  
        // otherwise the operation behavior is undefined.  
        req_ = {};  
        // Open a file to store the request body  
          
  
        req_.body().open("temp", beast::file_mode::write, file_ec);  
        if (file_ec) {  
            std::cerr << "Failed to open file: " << file_ec.message() << std::endl;  
            return;  
        }  
  
        // Read a request  
        http::async_read(stream_, buffer_, req_,  
            beast::bind_front_handler(  
                &session::on_read,  
                shared_from_this()));  
    }  
  
    void  
        on_read(  
            beast::error_code ec,  
            std::size_t bytes_transferred)  
    {  
        // Close the file to ensure data is flushed to disk  
        req_.body().close();  
        boost::ignore_unused(bytes_transferred);  
  
        if (ec == http::error::end_of_stream)  
        {  
            return do_close();  
        }  
        if (ec)  
        {  
            return fail(ec, "read");  
        }  
  
          
  
        // Reopen the file to get the file size  
        beast::error_code file_ec;  
        req_.body().open("temp", beast::file_mode::scan, file_ec);  
        if (file_ec) {  
            std::cerr << "Failed to reopen file: " << file_ec.message() << std::endl;  
            return;  
        }  
  
        // Get the file size  
        std::uint64_t file_size = req_.body().size();  
        // Log client IP and current time  
        tcp::endpoint remote_endpoint = stream_.next_layer().socket().remote_endpoint();  
        std::string client_ip = remote_endpoint.address().to_string();  
        boost::posix_time::ptime currentTime = boost::posix_time::second_clock::local_time();  
        async_log(client_ip, currentTime);  
  
        // Send the response  
        send_response(  
            handle_request_t(*doc_root_, std::move(req_)));  
    }  
  
    void  
        send_response(http::message_generator&& msg)  
    {  
        bool keep_alive = msg.keep_alive();  
  
        // Write the response  
        beast::async_write(  
            stream_,  
            std::move(msg),  
            beast::bind_front_handler(  
                &session::on_write,  
                this->shared_from_this(),  
                keep_alive));  
    }  
  
    void  
        on_write(  
            bool keep_alive,  
            beast::error_code ec,  
            std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if (ec)  
        {  
            return fail(ec, "write");  
        }  
        if (!keep_alive)  
        {  
            // This means we should close the connection  
            return do_close();  
        }  
        // Read another request    So keep_alive is meaningless now...  
     //   do_read();  
    }  
  
    void  
        do_close()  
    {  
        // Perform the SSL shutdown  
        stream_.async_shutdown(  
            beast::bind_front_handler(  
                &session::on_shutdown,  
                shared_from_this()));  
    }  
  
    void  
        on_shutdown(beast::error_code ec)  
    {  
        if (ec)  
            return fail(ec, "shutdown");  
  
        // At this point the connection is closed gracefully  
    }  
};`
