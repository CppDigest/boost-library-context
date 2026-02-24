# #292 - boost::asio::yield_context copy constructor noexcept [Closed]

> Username: omartijn  
> Created at: 2019-10-17 13:48:33 UTC  
> Updated at: 2020-12-30 01:10:22 UTC  
> Closed at: 2020-12-30 01:10:21 UTC  
> Url: https://github.com/boostorg/asio/issues/292  

As far as I can tell we should take a `boost::asio::yield_context` by value and not by reference. This then presents an issue when you want to enforce that a specific function must be `noexcept`, because this calls the copy-constructor for the `yield_context`, which is not `noexcept` qualified.  
  
Failing example:  
  
```  
#include <boost/asio/spawn.hpp>  
#include <type_traits>  
void x(boost::asio::yield_context yield) noexcept;  
int main()  
{  
    static_assert(std::is_nothrow_invocable_v<decltype(x), boost::asio::yield_context>);  
}  
```  
  
This can be solved by changing the parameter to `x` to take by reference instead of by value, but I get the impression that this goes against the design of the library.  
  
Looking at the code, I don't really see a reason for the copy-constructor to not be `noexcept`. Could this be changed?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:10:20 UTC  
> Url: https://github.com/boostorg/asio/issues/292#issuecomment-752293059  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#725](https://github.com/chriskohlhoff/asio/issues/725).
