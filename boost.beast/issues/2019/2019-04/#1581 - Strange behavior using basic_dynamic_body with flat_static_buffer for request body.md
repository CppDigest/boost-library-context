# #1581 - Strange behavior using basic_dynamic_body with flat_static_buffer for request body [Closed]

> Username: freak82  
> Created at: 2019-04-20 09:13:23 UTC  
> Updated at: 2019-07-09 18:02:15 UTC  
> Closed at: 2019-07-09 18:02:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1581  

### Version of Beast  
  
I'm using the beast version from boost 1.69.  
The `<boost/beast/version.hpp>` defines `#define BOOST_BEAST_VERSION 189`.  
  
### Steps necessary to reproduce the problem  
  
I'm just playing at the moment with boost::beast and I'm observing strange behavior if I use   
`flat_static_buffer` for the request body and the request body data has bigger size than the buffer can hold.  
Here is my the code for my session (I'm just getting used to the library, this is not production code):  
```  
class session : public std::enable_shared_from_this<session>                      
{                                                                                 
    using request_body_t =                                                        
        beast::http::basic_dynamic_body<beast::flat_static_buffer<16>>;           
    // beast::http::string_body;                                                  
                                                                                  
    baio::ip::tcp::socket sock_;                                                  
    beast::flat_static_buffer<2048> buff_;                                        
    beast::http::request<request_body_t> req_;                                    
                                                                                  
public:                                                                           
    explicit session(baio::ip::tcp::socket&& sock) noexcept                       
    : sock_{std::move(sock)}                                                      
    {                                                                             
    }                                                                             
                                                                                  
    void run() noexcept { read_req(); }                                           
                                                                                  
private:                                                                          
    void read_req() noexcept                                                      
    {                                                                             
        beast::http::async_read(                                                  
            sock_, buff_, req_,                                                   
            [s = shared_from_this()](const bsys::error_code& ec, size_t) {        
                if (ec)                                                           
                {                                                                 
                    std::cout << "Async_read: " << ec.message() << '\n';          
                    if (ec == beast::http::error::end_of_stream)                  
                        s->shutdown_conn();                                       
                    return;                                                       
                }                                                                 
                std::cout << "Got request. Method: " << s->req_.method()          
                          << ". URL: " << s->req_.target()                        
                          << ". Keep_alive: " << s->req_.keep_alive()             
                          << ". Full request:\n"                                  
                          << s->req_;                                             
            });                                                                   
    }                                                                             
                                                                                  
    void shutdown_conn() noexcept                                                 
    {                                                                             
        bsys::error_code skip;                                                    
        sock_.shutdown(baio::socket_base::shutdown_send, skip);                   
    }                                                                             
};  
```  
  
I'm using curl as http client.  
This POST request  
`curl -v --data "aaaaaaaaaaaaaa" http://127.0.0.1:4321/bbbbb`  
gives the expected behavior and the beast http server prints:  
```  
Got request. Method: POST. URL: /bbbbb. Keep_alive: 1. Full request:  
POST /bbbbb HTTP/1.1  
Host: 127.0.0.1:4321  
User-Agent: curl/7.58.0  
Accept: */*  
Content-Length: 14  
Content-Type: application/x-www-form-urlencoded  
  
```  
  
However, if I increase the size of the posted data so that it's bigger than the max allowed size (16 bytes) for the request body:  
`curl -v --data "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa" http://127.0.0.1:4321/bbbbb`  
then the server prints nothing. The handler is not called at all.  
I would expect to get `buffer overflow error` printed in the server console.  
  
### All relevant compiler information  
  
I'm using GCC 7.3 with `-std=c++17 -O2`.  
  
### More information  
I think the problem lays in `beast/http/basic_dynamic_body.hpp` there is a code in the function  
`std::size_t  put(ConstBufferSequence const& buffers, error_code& ec)` which is  
```  
if(body_.size() > body_.max_size() - n)                               
{                                                                     
    ec = error::buffer_overflow;                                      
    return 0;                                                         
}  
```  
As far as I debugged the issue, the `if` condition here is not evaluated to true due to wrapping in the unsigned integer calculations:  
`body_.size` is 0, `body_.max_size` is 16 and `n` is 52.  
If I change the condition to `if(body_.max_size() - body_.size() < n)`  
then my beast http server correctly prints `Async_read: buffer overflow` when I issue the second provided curl request.  
  
As far as I checked the relevant `if` condition is the same in the development branch of beast.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-05-21 04:31:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1581#issuecomment-494235328  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: freak82  
> Created at: 2019-05-21 05:19:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1581#issuecomment-494243459  

Doesn't seem to be be resolved.  
As far as I checked the `develop` branch, the wrapping condition is still there:  
`if(body_.size() > body_.max_size() - n)`

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-06-20 06:17:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1581#issuecomment-503891367  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: freak82  
> Created at: 2019-06-20 06:23:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1581#issuecomment-503892939  

Doesn't seem to be be resolved.  
As far as I checked the develop branch (`https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/basic_dynamic_body.hpp`), the wrapping condition is still there:  
`if(body_.size() > body_.max_size() - n)`.  
  
I don't know what to do at this point. Did I misunderstand something and this is not a bug? Or maybe my bug report is not clear?   
I understand that this is a very rare use case scenario and the probability for this condition to be hit is very low but on the other hand it seems to me as one line fix, if I'm not mistaken?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-06-20 12:35:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1581#issuecomment-504008520  

Thank you for the detailed bug report. I am investigating....
