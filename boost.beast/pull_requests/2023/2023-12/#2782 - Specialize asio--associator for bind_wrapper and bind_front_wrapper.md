# #2782 Specialize asio::associator for bind_wrapper and bind_front_wrapper [Merged]

> Username: ashtum  
> Created at: 2023-12-28 19:50:51 UTC  
> Updated at: 2023-12-29 09:40:30 UTC  
> Merged at: 2023-12-29 09:40:30 UTC  
> Closed at: 2023-12-29 09:40:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2782  

The existing code attempts to customize `asio::associated_executor` and other associators for `bind_wrapper` and `bind_front_wrapper`, leading to a situation where Asio consistently assumes the presence of an associated executor with the bound handlers. This results in [asio::detail::is_work_dispatcher_required](https://github.com/boostorg/asio/blob/2e49b21732e5d1bb3bb98f209164c30816b0bc79/include/boost/asio/detail/work_dispatcher.hpp#L42) always being set to true. Consequently, Asio wraps the bound handlers in [asio::detail::work_dispatcher](https://github.com/boostorg/asio/blob/2e49b21732e5d1bb3bb98f209164c30816b0bc79/include/boost/asio/detail/initiate_post.hpp#L148) before execution.  
  
Consequently, everything wrapped in a `bind_wrapper` or `bind_front_wrapper` is scheduled twice on the executor:  
```C++  
#include <boost/asio/bind_immediate_executor.hpp>  
#include <boost/asio/dispatch.hpp>  
#include <boost/beast/core/bind_handler.hpp>  
  
#include <iostream>  
  
using namespace boost;  
  
class immediate_executor  
{  
  std::size_t& count_;  
  
public:  
  immediate_executor(std::size_t& count) noexcept  
    : count_(count)  
  {  
  }  
  
  asio::execution_context& query(asio::execution::context_t) const  
  {  
    BOOST_ASSERT(false);  
    return *static_cast<asio::execution_context*>(nullptr);  
  }  
  
  constexpr static asio::execution::blocking_t query(asio::execution::blocking_t) noexcept  
  {  
    return asio::execution::blocking_t::never_t{};  
  }  
  
  constexpr static asio::execution::relationship_t query(asio::execution::relationship_t) noexcept  
  {  
    return asio::execution::relationship_t::fork_t{};  
  }  
  
  template<class F>  
  void execute(F f) const  
  {  
    count_++;  
    std::forward<F>(f)();  
  }  
  
  bool operator==(immediate_executor const&) const noexcept  
  {  
    return true;  
  }  
  
  bool operator!=(immediate_executor const&) const noexcept  
  {  
    return false;  
  }  
};  
  
int main()  
{  
  std::size_t ic = 0;  
  immediate_executor imex{ ic };  
  
  asio::dispatch(imex, beast::bind_handler([] {}));  
  
  std::cout << ic << std::endl; // prints 2  
}  
  
```  
Open in Compiler Explorer: [Link](https://godbolt.org/z/91r73EaMd)

---
