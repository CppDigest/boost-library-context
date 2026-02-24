# #2612 - Problem Creating Server Ctx Struct: Boost::Beast Way to write a Server_Ctx Struct [Closed]

> Username: WarrenN1  
> Created at: 2023-01-04 11:04:53 UTC  
> Updated at: 2023-10-28 16:34:46 UTC  
> Closed at: 2023-10-28 16:34:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2612  

Effectively, my boost beast web server I have been working on for a while has multiple aspects to it including a io_ctx, thread pool, database management system, and ssl context etc. so I am trying to rewrite functions to use a server_ctx struct that effectively stores all these variables for me so I can access them easier. So I wrote the following:  
```  
...  
//------------------------------------------------------------------------------  
namespace beast  = boost::beast;      // from <boost/beast.hpp>  
namespace http   = beast::http;          // from <boost/beast/http.hpp>  
namespace asio   = boost::asio;          // from <boost/asio.hpp>  
namespace ssl    = boost::asio::ssl;     // from <boost/asio/ssl.hpp>  
namespace tuples = boost::tuples;     // from <boost/tuple/tuple.hpp>  
namespace json   = boost::json;          // from <boost/json.hpp>  
using tuples::tie;  
using asio::awaitable;  
using asio::use_awaitable;  
using asio::detached;  
using asio::experimental::as_tuple;  
using asio::experimental::as_single;  
using asio::redirect_error;  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
using namespace boost::urls;          // from <boost/urls.hpp> - Expiremental  
//------------------------------------------------------------------------------  
struct cassandra_ctx_t  
{  
    CassCluster* cluster;  
    CassSession* session;  
};  
struct server_ctx_t  
{  
    asio::io_context   io_ctx;  
    asio::thread_pool  tp_ctx;  
    cassandra_ctx_t    cassandra_ctx;  
    ssl::context       ssl_ctx;  
    std::shared_ptr<std::string const> const doc_root;  
      
    server_ctx_t(asio::io_context&    io_ctx_,  
                 asio::thread_pool&   tp_ctx_,  
                 cassandra_ctx_t&     cassandra_ctx_,  
                 ssl::context&        ssl_ctx_,  
                 std::shared_ptr<std::string const> const& doc_root_)  
                 :   
                 io_ctx(io_ctx_),  
                 tp_ctx(tp_ctx_),  
                 cassandra_ctx(cassandra_ctx_),  
                 ssl_ctx(ssl_ctx_),  
                 doc_root(doc_root_)  
    {  
    };  
};  
```  
then I try and use this constructor in main via:  
```  
    struct server_ctx_t server_ctx(std::ref(io_ctx),  
                                   std::ref(tp_ctx),  
                                   std::ref(cassandra_ctx),  
                                   std::ref(ssl_ctx),  
                                   doc_root);  
```  
But I get the following error  
```  
In file included from main.cpp:1:  
/home/warrenniles/Desktop/project/project-webserver/project-webserver/server.hpp: In constructor ‘server_ctx_t::server_ctx_t(boost::asio::io_context&, boost::asio::thread_pool&, cassandra_ctx_t&, boost::asio::ssl::context&, const std::shared_ptr<const std::__cxx11::basic_string<char> >&)’:  
/home/warrenniles/Desktop/project/project-webserver/project-webserver/server.hpp:100:18: error: use of deleted function ‘boost::asio::io_context::io_context(const boost::asio::io_context&)’  
  100 |                  io_ctx(io_ctx_),  
      |                  ^~~~~~~~~~~~~~~  
In file included from /home/warrenniles/Desktop/project/project-webserver/external_libraries/include/boost/asio/detail/io_object_impl.hpp:23,  
                 from /home/warrenniles/Desktop/project/project-webserver/external_libraries/include/boost/asio/basic_waitable_timer.hpp:24,  
                 from /home/warrenniles/Desktop/project/project-webserver/external_libraries/include/boost/asio/steady_timer.hpp:22,  
                 from /home/warrenniles/Desktop/project/project-webserver/external_libraries/include/boost/beast/core/detail/stream_base.hpp:13,  
                 from /home/warrenniles/Desktop/project/project-webserver/external_libraries/include/boost/beast/core/basic_stream.hpp:14,  
                 from /home/warrenniles/Desktop/project/project-webserver/external_libraries/include/boost/beast/core.hpp:16,  
                 from /home/warrenniles/Desktop/project/project-webserver/project-webserver/server.hpp:25,  
                 from main.cpp:1:  
/home/warrenniles/Desktop/project/project-webserver/external_libraries/include/boost/asio/io_context.hpp:633:3: note: declared here  
  633 |   io_context(const io_context&) BOOST_ASIO_DELETED;  
      |   ^~~~~~~~~~  
```  
The problem is that this doesn't seem to be feasible, but I need a clean way to do this. What would be an appropriate boost beast way to do this so I can use functions like:  
```  
template<class Body, class Allocator,class Send>  
awaitable<void> handle_request(server_ctx_t& server_ctx,  
                               http::request<Body, http::basic_fields<Allocator>>&& req,  
                               Send&& send);  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-01-04 11:13:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2612#issuecomment-1370792633  

Why an asio::thread_pool & an asio::io_context? And why are you trying to copy them?  
  
The latter is the error.  
  
Also, why `std::ref` ?

---

## Comment 2

> Username: WarrenN1  
> Created at: 2023-01-04 11:43:35 UTC  
> Updated at: 2023-01-04 11:46:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2612#issuecomment-1370825977  

@klemens-morgenstern   
> Why an asio::thread_pool & an asio::io_context?  
  
Because some work is blocking bc I'm using 3rd party libraries so that is going to the thread Pool while io is handled on loop.  
  
> And why are you trying to copy them?  
>   
>   
>   
> The latter is the error.  
  
So the problem I'm having is the following: I want to package a bunch of these different variables into 1 data structure that I can pass to functions like handle request. My problems are as follows:  
1) The problem is I can't simply write a struct that has let's say asio::io_context io_ctx as a member and then in main write something like  
```  
Asio::io_context io_context{5};  
struct server_ctx myServerCtx;  
server_ctx.io_ctx= io_ctx;  
```  
Because for example ssl::context requires passing tls version to the constructor.  
2) So I know I can theoretically initialize members before the body of the constructor executes to try and get around this. Problem is I don't want to have to include all that logic within my constructor of setting the number of io threads. Setting the number of threads for the thread Pool for handling old legacy stuff etc.  
  
I thought maybe copying once could get me the behavior I wanted but that hasn't worked either.  
  
All I want essentially is to set up the asio::io_context, asio::thread_pool, ssl::context etc. In main.cpp, load them into 1 data structure that allows me to pass them all as references to various functions. I'm just having one hell of a time figuring out the best way to go about getting this desired behavior.  
  
> Also, why `std::ref` ?  
  
Because I'm trying to avoid the hell that is pointer notation.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-01-04 12:01:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2612#issuecomment-1370841348  

I don't follow, but that's alright.  
  
The answer is simple you can neither copy nor move an io_context nor a thead_pool for that matter.    
  
What might work for you is to not pass the io_context but an io_context::executor_type (same for the pool) into the server struct, i.e.:  
  
```cpp  
  
struct my_server  
{  
    asio::io_context::executor_type io_executor;  
  
    my_server(asio::io_context & ctx) : io_executor(ctx.get_executor()) {}  
};  
  
// ... other stuff  
  
my_server srv(ioc1);  
  
srv.io_executor = ioc2.get_executor();  
```  
  
The executor can also be of type `asio::any_io_executor` if you need it type erased. I think this is much closer to what you probably want; I am however guessing a bit here.

---

## Comment 4

> Username: WarrenN1  
> Created at: 2023-01-04 12:07:50 UTC  
> Updated at: 2023-01-04 12:08:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2612#issuecomment-1370848666  

@klemens-morgenstern Yeah I guess if I can't copy or move it then that would probably be as close as I would get to the behavior i am looking for.  
I was just hoping there was a way where instead of writing:  
  
```  
handle_request(asio::io_context& io_ctx, asio::threadpool& tp_ctx, ssl::context& ssl_context, ...)  
```  
I could write:  
  
```  
handle_request(struct server_ctx&, ...)  
```  
  
So what your saying is there is no way to go about getting that desired result of moving all the io_ctx tp_ctx etc into 1 struct to pass by reference without for example creating a constructor that handles all the server configuration in advance or using the executor method?

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2023-01-04 12:31:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2612#issuecomment-1370871326  

You cannot move execution_contexts such as `io_context` and `thread_pool`. That won't ever change, but you can use executors, which I think are made for your use-case.

---

## Comment 6

> Username: ashtum  
> Created at: 2023-08-18 13:41:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2612#issuecomment-1683940264  

@WarrenN1 Is this still open?
