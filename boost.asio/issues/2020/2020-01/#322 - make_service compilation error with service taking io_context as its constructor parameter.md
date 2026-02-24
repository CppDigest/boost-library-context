# #322 - make_service compilation error with service taking io_context as its constructor parameter [Closed]

> Username: NightWulfe  
> Created at: 2020-01-23 02:35:54 UTC  
> Updated at: 2020-12-30 01:12:43 UTC  
> Closed at: 2020-12-30 01:12:42 UTC  
> Url: https://github.com/boostorg/asio/issues/322  

I created a service object which inherits from `boost::asio::io_context::service`. It has a constructor which takes a `boost::asio::io_context&` and a `std::string_view`. The string_view is required as the service needs some configuration values that apply to all `basic_io_object` instances which use it.  
  
```  
class my_service : public boost::asio::io_context::service {  
public:  
    boost::asio::io_context::id id;  
    my_service(boost::asio::io_context& context, std::string_view str) :  
        service(context)  
    { }  
}  
```  
  
At application startup, I attempt to initialize the service for the first time:  
```  
boost::asio::io_context context;  
boost::asio::make_service<my_service>(context, "Test");  
```  
  
This fails to compile as `my_service` takes an `io_context` reference as the first parameter instead of an `execution_context` reference. I noticed `boost::asio::use_service` has an overload for `io_context`. Is this overload missing for `make_service`, or am I using it `make_service` incorrectly?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:12:41 UTC  
> Url: https://github.com/boostorg/asio/issues/322#issuecomment-752293393  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#744](https://github.com/chriskohlhoff/asio/issues/744).
