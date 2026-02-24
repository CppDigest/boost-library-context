# #61 - Noncopyable (move-only) completion handlers are unsupported [Closed]

> Username: djarek  
> Created at: 2017-11-05 20:27:30 UTC  
> Updated at: 2017-12-02 08:15:49 UTC  
> Closed at: 2017-12-02 08:15:49 UTC  
> Url: https://github.com/boostorg/asio/issues/61  

Example:  
```  
#include <boost/asio/io_context.hpp>  
#include <memory>  
int main()  
{  
    boost::asio::io_context ctx;  
    struct op  
    {  
        std::unique_ptr<int> data;  
        void operator()() { }  
    };  
    ctx.post(op{});  
}  
```  
fails to compile:  
```  
/usr/local/include/boost/asio/impl/io_context.hpp:174:3: error: use of deleted function ‘main()::op::op(const main()::op&)’  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-05 20:27:57 UTC  
> Url: https://github.com/boostorg/asio/issues/61#issuecomment-342002685  

Also move-only handlers are unsupported :)

---

## Comment 2

> Username: chriskohlhoff  
> Created at: 2017-12-02 08:15:49 UTC  
> Url: https://github.com/boostorg/asio/issues/61#issuecomment-348676689  

Use the free functions `asio::post()` etc. These old (and now deprecated) interfaces still require copy construction as they use the pre-executor handler mechanism.
