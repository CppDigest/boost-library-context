# #340 - SFINAE issue get_composed_io_executor (async_compose) [Closed]

> Username: xaqq  
> Created at: 2020-04-08 14:12:49 UTC  
> Updated at: 2020-12-30 01:13:49 UTC  
> Closed at: 2020-12-30 01:13:48 UTC  
> Url: https://github.com/boostorg/asio/issues/340  

Hello,  
I encountered an issue when using `async_compose`. This issue occurs when using gcc, but the code compiles fine with clang.  
  
It looks like the wrong overload of `get_composed_io_executor()`  ends up being selected by SFINAE causing compilation error.  
  
Consider the following example:  
  
```  
#include <boost/asio.hpp>  
  
template <typename Executor, typename CompletionToken>  
void async_gcc_issue(Executor &executor, CompletionToken &&token)  
{  
    return boost::asio::async_compose<CompletionToken, void(boost::system::error_code)>(  
        [](auto &self) { self.complete({}); }, token, executor);  
}  
  
int main()  
{   
    boost::asio::io_context ioc_;  
    boost::asio::io_context::executor_type ex  = ioc_.get_executor();  
    static_assert(boost::asio::detail::is_executor<decltype(ex)>::value);  
    async_gcc_issue(ex, [](auto ec) {});  
}  
  
```  
  
It seems fixable by changing   
  
```  
template <typename IoObject>  
inline typename IoObject::executor_type  
get_composed_io_executor(IoObject &io_object ){  
  return io_object.get_executor();  
}  
```  
  
to   
```  
template <typename IoObject>  
inline typename IoObject::executor_type  
get_composed_io_executor(IoObject &io_object,   
typename enable_if<!is_executor<IoObject>::value>::type * = 0) {  
  return io_object.get_executor();  
}  
```  
  
However I am not sure of the implication of this change.  
Its possible that I misuse the library causing other issue.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:13:47 UTC  
> Url: https://github.com/boostorg/asio/issues/340#issuecomment-752293563  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#757](https://github.com/chriskohlhoff/asio/issues/757).
