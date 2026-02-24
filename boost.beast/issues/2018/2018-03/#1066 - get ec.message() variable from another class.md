# #1066 - get ec.message() variable from another class [Closed]

> Username: Ludea  
> Created at: 2018-03-11 19:38:24 UTC  
> Updated at: 2018-05-24 23:08:06 UTC  
> Closed at: 2018-05-24 23:08:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1066  

Hello,  
  
I follow the http over ssh examples.  
But, for my app, I have to get the value returned by the fail function.  
So I change a little the code, I add `fail` into request class (public), and a std::string variable.  
The std string get the error message.   
When I call the string into another class:  
```  
request* req;  
printf(req->mystringvalue);   
```  
, there are no values.   
So can I get the error message from another class ?  
  
Thanks !

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-11 20:22:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1066#issuecomment-372145942  

I'm going to need a little more information than that, do you have an example program which I can run that exhibits the behavior you are describing?

---

## Comment 2

> Username: Ludea  
> Created at: 2018-03-12 05:52:00 UTC  
> Updated at: 2018-03-12 05:52:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1066#issuecomment-372200571  

Yes sorry .  
  
request cpp  
```  
void request::fail(boost::system::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
    std::cout<<ec.message()<<std::endl;  
    respond = ec.message() ;  
}  
  
void request::start_request(char const* host, char const* port, char const* target, int version, std::tring method, std::string body)  
{  
    boost::asio::io_context ioc;  
    ssl::context ctx{ssl::context::sslv23_client};  
    std::make_shared<request>(ioc, ctx)->run(host, port, target, version, method, body);  
    ioc.run();  
}  
```  
  
request .h  
```  
public:  
std::string respond;  
void fail(boost::system::error_code ec, char const* what);  
```  
  
my class which call respond  
```  
void connection::server_connect(std::tring host, std::string port, std::string target, int version, std::string method, std::string body)  
{  
    http->start_request(host, port, target, version, method, body);  
}  
  
std::string connection::getHttpResponseBody()  
{  
    std::cout<<"test : "<<http->respond<<std::endl;  
    return (http->respond);  
}  
```  
  
.h  
```  
private:  
    request *http;  
````

---

## Comment 3

> Username: Ludea  
> Created at: 2018-03-12 17:54:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1066#issuecomment-372404319  

If I initialize my string var into constructor, i can get his values.  
But no ec.message() values

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1066#issuecomment-384003546  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-04-24 22:06:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1066#issuecomment-384095360  

Are you linking dynamically to Boost.System? Perhaps this is a problem with a mismatch between the shared library version and the header file version. Try linking everything statically.

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-05-24 22:56:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1066#issuecomment-391890879  

This issue has been open for a while with no activity, has it been resolved?
