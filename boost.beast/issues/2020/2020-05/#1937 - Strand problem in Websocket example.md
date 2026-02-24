# #1937 - Strand problem in Websocket example [Closed]

> Username: baker-Xie  
> Created at: 2020-05-04 11:26:13 UTC  
> Updated at: 2020-05-09 05:29:01 UTC  
> Closed at: 2020-05-04 14:19:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1937  

Hi, forgive me, this is not a bug but a question.   
Beast and asio are great libraries, its so powerful that I spent serveral times to learn it with document and google. But I still can't get my question solved so I have no idea but to post my question here.   
  
In websocket_server_async.cpp  
```  
// Get on the correct executor  
    void  
    run()  
    {  
        // We need to be executing within a strand to perform async operations  
        // on the I/O objects in this session. Although not strictly necessary  
        // for single-threaded contexts, this example code is written to be  
        // thread-safe by default.  
        net::dispatch(ws_.get_executor(),  
            beast::bind_front_handler(  
                &session::on_run,  
                shared_from_this()));  
    }  
```  
and  
```  
void  
    do_accept()  
    {  
        // The new connection gets its own strand  
        acceptor_.async_accept(  
            net::make_strand(ioc_),  
            beast::bind_front_handler(  
                &listener::on_accept,  
                shared_from_this()));  
    }  
```  
I only see strand in the code part 2 but I didn't see strand in the code part 1. But the comment says it used strand in code part 1. Why?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-05-04 11:43:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1937#issuecomment-623414784  

HI @baker-Xie , thanks for getting in touch.  
  
There are a few concepts to understand here.  
  
* _implicit strand_ means that by design only one thread of execution will seek to access a resource. This could be because the program is single-threaded, or because only one thread has been assigned to running an io_context. It could also mean that although there are multiple threads running an io_context, it is guaranteed that only one of them will touch a given resource at any one time. This is often the case with an `async_accept` loop. It is illegal for more than one async_accept operation to be in progress per acceptor at a time.  
  
* _explicit strand_ means that the executor associated with an asynchronous completion handler or io_object is actually a strand. It can also mean that the resource is protected by a mutex.  
  
In the second example, there is an _explicit strand_ assigned to the socket accepted by the async_accept operation.  
  
In the first example, it is assumed that either there is an _implicit strand_ in the program's design _or_ that the executor of the websocket stream is an _explicit strand_, e.g. `net::strand<net::executor>` or `net::strand<net::io_context::executor_type>`  
  
Note that the polymorphic wrapper `net::executor` may contain any model of Executor, and a `net::strand` _is a model of_ Executor.

---

## Comment 2

> Username: baker-Xie  
> Created at: 2020-05-05 00:49:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1937#issuecomment-623783753  

> HI @baker-Xie , thanks for getting in touch.  
>   
> There are a few concepts to understand here.  
>   
> * _implicit strand_ means that by design only one thread of execution will seek to access a resource. This could be because the program is single-threaded, or because only one thread has been assigned to running an io_context. It could also mean that although there are multiple threads running an io_context, it is guaranteed that only one of them will touch a given resource at any one time. This is often the case with an `async_accept` loop. It is illegal for more than one async_accept operation to be in progress per acceptor at a time.  
> * _explicit strand_ means that the executor associated with an asynchronous completion handler or io_object is actually a strand. It can also mean that the resource is protected by a mutex.  
>   
> In the second example, there is an _explicit strand_ assigned to the socket accepted by the async_accept operation.  
>   
> In the first example, it is assumed that either there is an _implicit strand_ in the program's design _or_ that the executor of the websocket stream is an _explicit strand_, e.g. `net::strand<net::executor>` or `net::strand<net::io_context::executor_type>`  
>   
> Note that the polymorphic wrapper `net::executor` may contain any model of Executor, and a `net::strand` _is a model of_ Executor.  
  
Hi madmongo, thanks for your replay. I still cannot understand what happened in the code.  
I think it's may be helpful to express my question to post all the important part of the code. Here is the example code in beast example websocket_server_async.cpp  
  
```  
void  
fail(beast::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Echoes back all received WebSocket messages  
class session : public std::enable_shared_from_this<session>  
{  
    websocket::stream<beast::tcp_stream> ws_;  
    beast::flat_buffer buffer_;  
  
public:  
    // Take ownership of the socket  
    explicit  
    session(tcp::socket&& socket)  
        : ws_(std::move(socket))  
    {  
    }  
  
    // Get on the correct executor  
    void  
    run()  
    {  
        // We need to be executing within a strand to perform async operations  
        // on the I/O objects in this session. Although not strictly necessary  
        // for single-threaded contexts, this example code is written to be  
        // thread-safe by default.  
        net::dispatch(ws_.get_executor(),  
            beast::bind_front_handler(  
                &session::on_run,  
                shared_from_this()));  
    }  
  
    // Start the asynchronous operation  
    void  
    on_run()  
    {  
        // Set suggested timeout settings for the websocket  
        ws_.set_option(  
            websocket::stream_base::timeout::suggested(  
                beast::role_type::server));  
  
        // Set a decorator to change the Server of the handshake  
        ws_.set_option(websocket::stream_base::decorator(  
            [](websocket::response_type& res)  
            {  
                res.set(http::field::server,  
                    std::string(BOOST_BEAST_VERSION_STRING) +  
                        " websocket-server-async");  
            }));  
        // Accept the websocket handshake  
        ws_.async_accept(  
            beast::bind_front_handler(  
                &session::on_accept,  
                shared_from_this()));  
    }  
  
    void  
    on_accept(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "accept");  
  
        // Read a message  
        do_read();  
    }  
  
    void  
    do_read()  
    {  
        // Read a message into our buffer  
        ws_.async_read(  
            buffer_,  
            beast::bind_front_handler(  
                &session::on_read,  
                shared_from_this()));  
    }  
  
    void  
    on_read(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        // This indicates that the session was closed  
        if(ec == websocket::error::closed)  
            return;  
  
        if(ec)  
            fail(ec, "read");  
  
        // Echo the message  
        ws_.text(ws_.got_text());  
        ws_.async_write(  
            buffer_.data(),  
            beast::bind_front_handler(  
                &session::on_write,  
                shared_from_this()));  
    }  
  
    void  
    on_write(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "write");  
  
        // Clear the buffer  
        buffer_.consume(buffer_.size());  
  
        // Do another read  
        do_read();  
    }  
};  
  
//------------------------------------------------------------------------------  
  
// Accepts incoming connections and launches the sessions  
class listener : public std::enable_shared_from_this<listener>  
{  
    net::io_context& ioc_;  
    tcp::acceptor acceptor_;  
  
public:  
    listener(  
        net::io_context& ioc,  
        tcp::endpoint endpoint)  
        : ioc_(ioc)  
        , acceptor_(ioc)  
    {  
        beast::error_code ec;  
  
        // Open the acceptor  
        acceptor_.open(endpoint.protocol(), ec);  
        if(ec)  
        {  
            fail(ec, "open");  
            return;  
        }  
  
        // Allow address reuse  
        acceptor_.set_option(net::socket_base::reuse_address(true), ec);  
        if(ec)  
        {  
            fail(ec, "set_option");  
            return;  
        }  
  
        // Bind to the server address  
        acceptor_.bind(endpoint, ec);  
        if(ec)  
        {  
            fail(ec, "bind");  
            return;  
        }  
  
        // Start listening for connections  
        acceptor_.listen(  
            net::socket_base::max_listen_connections, ec);  
        if(ec)  
        {  
            fail(ec, "listen");  
            return;  
        }  
    }  
  
    // Start accepting incoming connections  
    void  
    run()  
    {  
        do_accept();  
    }  
  
private:  
    void  
    do_accept()  
    {  
        // The new connection gets its own strand  
        acceptor_.async_accept(  
            net::make_strand(ioc_),  
            beast::bind_front_handler(  
                &listener::on_accept,  
                shared_from_this()));  
    }  
  
    void  
    on_accept(beast::error_code ec, tcp::socket socket)  
    {  
        if(ec)  
        {  
            fail(ec, "accept");  
        }  
        else  
        {  
            // Create the session and run it  
            std::make_shared<session>(std::move(socket))->run();  
        }  
  
        // Accept another connection  
        do_accept();  
    }  
};  
```  
  
Code Part 2  
```  
void do_accept()  
{  
        // The new connection gets its own strand  
        acceptor_.async_accept(  
            net::make_strand(ioc_),  
            beast::bind_front_handler(  
                &listener::on_accept,  
                shared_from_this()));  
}  
```  
  
Code Part 1  
```  
// Get on the correct executor  
    void  
    run()  
    {  
        // We need to be executing within a strand to perform async operations  
        // on the I/O objects in this session. Although not strictly necessary  
        // for single-threaded contexts, this example code is written to be  
        // thread-safe by default.  
        net::dispatch(ws_.get_executor(),  
            beast::bind_front_handler(  
                &session::on_run,  
                shared_from_this()));  
    }  
```  
So here are my questions:  
(1) In code(Part 2), a strand is declared explictly, so I can understand that the "on_accept" and other hide handler in "net::make_strand(ioc_)" will be executed by the "net::make_strand(ioc_)". Is that right? And where are the other hide handler here?  
(2) In code(Part 1), the ws_ object is only constructed by a socket,  
```  
    explicit session(tcp::socket&& socket):  
    _remote_address(socket.remote_endpoint().address().to_string()),  
    _remote_port(socket.remote_endpoint().port()),  
    ws_(std::move(socket))  
    {  
        _endpoint_str = _remote_address + ":" + std::to_string(_remote_port);  
    }  
```  
so, is the ws_.get_executor() a strand or normal IO_context? If it's a strand, where is this strand from?  
(3) Meanwhile, in class session, will the async_read/async_write/async_accept executed in sequence?

---

## Comment 3

> Username: baker-Xie  
> Created at: 2020-05-05 07:24:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1937#issuecomment-623896494  

> HI @baker-Xie , thanks for getting in touch.  
>   
> There are a few concepts to understand here.  
>   
> * _implicit strand_ means that by design only one thread of execution will seek to access a resource. This could be because the program is single-threaded, or because only one thread has been assigned to running an io_context. It could also mean that although there are multiple threads running an io_context, it is guaranteed that only one of them will touch a given resource at any one time. This is often the case with an `async_accept` loop. It is illegal for more than one async_accept operation to be in progress per acceptor at a time.  
> * _explicit strand_ means that the executor associated with an asynchronous completion handler or io_object is actually a strand. It can also mean that the resource is protected by a mutex.  
>   
> In the second example, there is an _explicit strand_ assigned to the socket accepted by the async_accept operation.  
>   
> In the first example, it is assumed that either there is an _implicit strand_ in the program's design _or_ that the executor of the websocket stream is an _explicit strand_, e.g. `net::strand<net::executor>` or `net::strand<net::io_context::executor_type>`  
>   
> Note that the polymorphic wrapper `net::executor` may contain any model of Executor, and a `net::strand` _is a model of_ Executor.  
  
Hi madmongo, after 10 hours of google, I think a have a deeper understanding of what you said.  
Code Part1:  
```  
void  
    do_accept()  
    {  
        // The new connection gets its own strand  
        acceptor_.async_accept(  
            net::make_strand(ioc_),  
            beast::bind_front_handler(  
                &listener::on_accept,  
                shared_from_this()));  
    }  
```  
In Code Part1, the do_accept() is designed to have a implicit strand because the next async_accpet operation is called in the on_accept handler, so only one thread can get the resourece. Is that right?  
  
Code Part2:  
```  
class session : public std::enable_shared_from_this<session>  
{  
    websocket::stream<beast::tcp_stream> ws_;  
    beast::flat_buffer buffer_;  
  
public:  
    // Take ownership of the socket  
    explicit  
    session(tcp::socket&& socket)  
        : ws_(std::move(socket))  
    {  
    }  
  
    // Get on the correct executor  
    void  
    run()  
    {  
        // We need to be executing within a strand to perform async operations  
        // on the I/O objects in this session. Although not strictly necessary  
        // for single-threaded contexts, this example code is written to be  
        // thread-safe by default.  
        net::dispatch(ws_.get_executor(),  
            beast::bind_front_handler(  
                &session::on_run,  
                shared_from_this()));  
    }  
  
    // Start the asynchronous operation  
    void  
    on_run()  
    {  
        // Set suggested timeout settings for the websocket  
        ws_.set_option(  
            websocket::stream_base::timeout::suggested(  
                beast::role_type::server));  
  
        // Set a decorator to change the Server of the handshake  
        ws_.set_option(websocket::stream_base::decorator(  
            [](websocket::response_type& res)  
            {  
                res.set(http::field::server,  
                    std::string(BOOST_BEAST_VERSION_STRING) +  
                        " websocket-server-async");  
            }));  
        // Accept the websocket handshake  
        ws_.async_accept(  
            beast::bind_front_handler(  
                &session::on_accept,  
                shared_from_this()));  
    }  
  
    void  
    on_accept(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "accept");  
  
        // Read a message  
        do_read();  
    }  
  
    void  
    do_read()  
    {  
        // Read a message into our buffer  
        ws_.async_read(  
            buffer_,  
            beast::bind_front_handler(  
                &session::on_read,  
                shared_from_this()));  
    }  
  
    void  
    on_read(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        // This indicates that the session was closed  
        if(ec == websocket::error::closed)  
            return;  
  
        if(ec)  
            fail(ec, "read");  
  
        // Echo the message  
        ws_.text(ws_.got_text());  
        ws_.async_write(  
            buffer_.data(),  
            beast::bind_front_handler(  
                &session::on_write,  
                shared_from_this()));  
    }  
  
    void  
    on_write(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "write");  
  
        // Clear the buffer  
        buffer_.consume(buffer_.size());  
  
        // Do another read  
        do_read();  
    }  
};  
```  
In Code Part2, ws_ is constructed by a socket ( which created in Code Part1 net::make_strand(ioc_), wo just name it socket A ), so the executor of ws_ is also a kind of strand, is that right?  
and if I call ws_.async_write / ws_.async_read / all these operation are all under socket A, and socket A is a kind of strand, so all these operation will be executed in order?

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-05-06 15:07:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1937#issuecomment-624705645  

@baker-Xie   
  
> > HI @baker-Xie , thanks for getting in touch.  
> > There are a few concepts to understand here.  
> >   
> > * _implicit strand_ means that by design only one thread of execution will seek to access a resource. This could be because the program is single-threaded, or because only one thread has been assigned to running an io_context. It could also mean that although there are multiple threads running an io_context, it is guaranteed that only one of them will touch a given resource at any one time. This is often the case with an `async_accept` loop. It is illegal for more than one async_accept operation to be in progress per acceptor at a time.  
> > * _explicit strand_ means that the executor associated with an asynchronous completion handler or io_object is actually a strand. It can also mean that the resource is protected by a mutex.  
> >   
> > In the second example, there is an _explicit strand_ assigned to the socket accepted by the async_accept operation.  
> > In the first example, it is assumed that either there is an _implicit strand_ in the program's design _or_ that the executor of the websocket stream is an _explicit strand_, e.g. `net::strand<net::executor>` or `net::strand<net::io_context::executor_type>`  
> > Note that the polymorphic wrapper `net::executor` may contain any model of Executor, and a `net::strand` _is a model of_ Executor.  
>   
> Hi madmongo, after 10 hours of google, I think a have a deeper understanding of what you said.  
> Code Part1:  
>   
> ```  
> void  
>     do_accept()  
>     {  
>         // The new connection gets its own strand  
>         acceptor_.async_accept(  
>             net::make_strand(ioc_),  
>             beast::bind_front_handler(  
>                 &listener::on_accept,  
>                 shared_from_this()));  
>     }  
> ```  
>   
> In Code Part1, the do_accept() is designed to have a implicit strand because the next async_accpet operation is called in the on_accept handler, so only one thread can get the resourece. Is that right?  
  
Yes, that's right.  
  
>   
> Code Part2:  
>   
> ```  
> class session : public std::enable_shared_from_this<session>  
> {  
>     websocket::stream<beast::tcp_stream> ws_;  
>     beast::flat_buffer buffer_;  
>   
> public:  
>     // Take ownership of the socket  
>     explicit  
>     session(tcp::socket&& socket)  
>         : ws_(std::move(socket))  
>     {  
>     }  
>   
>     // Get on the correct executor  
>     void  
>     run()  
>     {  
>         // We need to be executing within a strand to perform async operations  
>         // on the I/O objects in this session. Although not strictly necessary  
>         // for single-threaded contexts, this example code is written to be  
>         // thread-safe by default.  
>         net::dispatch(ws_.get_executor(),  
>             beast::bind_front_handler(  
>                 &session::on_run,  
>                 shared_from_this()));  
>     }  
>   
>     // Start the asynchronous operation  
>     void  
>     on_run()  
>     {  
>         // Set suggested timeout settings for the websocket  
>         ws_.set_option(  
>             websocket::stream_base::timeout::suggested(  
>                 beast::role_type::server));  
>   
>         // Set a decorator to change the Server of the handshake  
>         ws_.set_option(websocket::stream_base::decorator(  
>             [](websocket::response_type& res)  
>             {  
>                 res.set(http::field::server,  
>                     std::string(BOOST_BEAST_VERSION_STRING) +  
>                         " websocket-server-async");  
>             }));  
>         // Accept the websocket handshake  
>         ws_.async_accept(  
>             beast::bind_front_handler(  
>                 &session::on_accept,  
>                 shared_from_this()));  
>     }  
>   
>     void  
>     on_accept(beast::error_code ec)  
>     {  
>         if(ec)  
>             return fail(ec, "accept");  
>   
>         // Read a message  
>         do_read();  
>     }  
>   
>     void  
>     do_read()  
>     {  
>         // Read a message into our buffer  
>         ws_.async_read(  
>             buffer_,  
>             beast::bind_front_handler(  
>                 &session::on_read,  
>                 shared_from_this()));  
>     }  
>   
>     void  
>     on_read(  
>         beast::error_code ec,  
>         std::size_t bytes_transferred)  
>     {  
>         boost::ignore_unused(bytes_transferred);  
>   
>         // This indicates that the session was closed  
>         if(ec == websocket::error::closed)  
>             return;  
>   
>         if(ec)  
>             fail(ec, "read");  
>   
>         // Echo the message  
>         ws_.text(ws_.got_text());  
>         ws_.async_write(  
>             buffer_.data(),  
>             beast::bind_front_handler(  
>                 &session::on_write,  
>                 shared_from_this()));  
>     }  
>   
>     void  
>     on_write(  
>         beast::error_code ec,  
>         std::size_t bytes_transferred)  
>     {  
>         boost::ignore_unused(bytes_transferred);  
>   
>         if(ec)  
>             return fail(ec, "write");  
>   
>         // Clear the buffer  
>         buffer_.consume(buffer_.size());  
>   
>         // Do another read  
>         do_read();  
>     }  
> };  
> ```  
>   
> In Code Part2, ws_ is constructed by a socket ( which created in Code Part1 net::make_strand(ioc_), wo just name it socket A ), so the executor of ws_ is also a kind of strand, is that right?  
> and if I call ws_.async_write / ws_.async_read / all these operation are all under socket A, and socket A is a kind of strand, so all these operation will be executed in order?  
  
Yes, the version of async_accept used in the second example takes an executor as an argument.  
  
When it accepts a connection, it creates a socket _which is associated with the executor argument_. That is to say, quite possibly a different executor to the one associated with the acceptor.  
  
In this case, we are passing a strand built on the acceptors executor. So the socket and acceptor share the same _underlying_ executor, but async operations on the socket (socket A, and therefore ws_) will complete as if being posted to the strand - which is to say one at a time.   
  
Note that it's not only the final completion handlers that are protected - all of the _intermediate_ completion handlers are executed on the strand too - which means that all the internal moving parts of the websocket stream type are guaranteed not to race for data.  
  
Asio is built on layers. In this case the layers are:  
  
application's completion handler  
websocket's intermediate completion handler  
underlying socket completion handler  
  
An async operation will walk down this stack during initiation of the operation, and back up it as the sub-operations complete.  
  
Note that there might be many intermediate completions against the socket and the websocket's timer before the application's completion handler is finally invoked.  
  
(note that I use the word invoke rather than call. handlers are invoked through the executor, not called directly)

---

## Comment 5

> Username: baker-Xie  
> Created at: 2020-05-07 01:18:40 UTC  
> Updated at: 2020-05-07 02:34:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1937#issuecomment-624973958  

Oh, thanks for your answer, it's very helpful. I think from now, I understand what does the code works.  
But I have a little more deeper question.  
1. As you said, the tcp::accept.async_accept and ws.async_accept have different executor, the first one is ioc_, the second one is net::make_strand(ioc_), right? And the first one will not be protected by strand because it have no starnd executor, right?   
2. In on_accept(), there is a parameter tcp::socket socket passed by value, so  
(1) Does the socketA constructed in async_accept? That is to say, some hidden code will construct a new socketA, so on the on_accept callback function I can get the socketA by parameter?  
(2) socketA have something like net::make_strand(ioc_), so the executor of this socket is strand, right? so if ws or other stream constructed by this socket, so all the executor of these stream is a starnd and will be protected by strand, is that right?  
(3) I learn something from google that the socket object cannot be copied. But in the on_accept handler, the socket parameter is passed by value, is it because the hidden code in async_accept use std::move(socket) ?  
```  
void do_accept()  
    {  
        // The new connection gets its own strand  
        acceptor_.async_accept(  
            net::make_strand(ioc_),  
            beast::bind_front_handler(  
                &listener::on_accept,  
                shared_from_this()));  
    }  
  
void on_accept(beast::error_code ec, tcp::socket socket)  // We just name this socket object socketA  
    {  
        if(ec)  
        {  
            PrintFail(ec, "accept");  
        }  
        else  
        {  
            // Create the session and run it  
            std::make_shared<session>(std::move(socket))->run();  
        }  
  
        // Accept another connection  
        do_accept();  
    }  
```

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-05-08 17:35:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1937#issuecomment-625930963  

On Thu, 7 May 2020 at 03:18, baker-Xie <notifications@github.com> wrote:  
  
> Oh, thanks for your answer, it's very helpful. I think from now, I  
> understand what does the code works.  
> But I have a little more deeper question.  
>  
>    1. As you said, the tcp::accept.async_accept and ws.async_accept have  
>    different executor, the first one is ioc_, the second one is  
>    net::make_strand(ioc_), right?  
>  
> This I don't think either are assocatated with an explicit strand. The  
`make_strand()` as an argument to `async_accept` is creating the executor  
that will be associated with the socket that gets delivered in the  
completion handler of the async_accept call.  
  
> And the first one will not be protected by strand because it have no  
starnd executor, right?  
  
right  
  
  
> In on_accept(), there is a parameter tcp::socket socket passed by value, so  
> (1) Does the socketA constructed in async_accept? That is to say, some  
> hidden code will construct a new socketA, so on the on_accept callback  
> function I can get the socketA by parameter?  
>  
  
yes. This version of the call creates a socket associated with the executor  
you passed in and delivers it to your completion handler, ready-bound.  
  
(2) socketA have something like net::make_strand(ioc_), so the executor of  
> this socket is strand, right? so if ws or other stream constructed by this  
> socket, so all the executor of these stream is a starnd and will be  
> protected by strand, is that right?  
>  
  
Yes  
  
  
> (3) I learn something from google that the socket object cannot be copied.  
> But in the on_accept handler, the socket parameter is passed by value, not  
> reference, why?  
>  
  
Because sockets are movable. You could just as easily write your completion  
handler like this:  
  
`void on_connect(error_code ec, socket&& sock)`  
  
  
> void do_accept()  
>     {  
>         // The new connection gets its own strand  
>         acceptor_.async_accept(  
>             net::make_strand(ioc_),  
>             beast::bind_front_handler(  
>                 &listener::on_accept,  
>                 shared_from_this()));  
>     }  
>  
> void on_accept(beast::error_code ec, tcp::socket socket)  // We just name this socket object socketA  
>     {  
>         if(ec)  
>         {  
>             PrintFail(ec, "accept");  
>         }  
>         else  
>         {  
>             // Create the session and run it  
>             std::make_shared<session>(std::move(socket))->run();  
>         }  
>  
>         // Accept another connection  
>         do_accept();  
>     }  
>  
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/1937#issuecomment-624973958>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSPRKZEZQQ2WC5T7HR3RQID7ZANCNFSM4MYVOWOQ>  
> .  
>  
  
  
--   
Richard Hodges  
hodges.r@gmail.com  
office: +442032898513  
home: +376841522  
mobile: +376380212

---

## Comment 7

> Username: baker-Xie  
> Created at: 2020-05-09 05:29:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1937#issuecomment-626109138  

@madmongo1 It's very nice of you. Thanks for you help!
