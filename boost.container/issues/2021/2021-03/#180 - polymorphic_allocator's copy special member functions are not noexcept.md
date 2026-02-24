# #180 - polymorphic_allocator's copy special member functions are not noexcept [Closed]

> Username: palebedev  
> Created at: 2021-03-08 19:06:41 UTC  
> Updated at: 2021-04-20 22:54:03 UTC  
> Closed at: 2021-04-20 22:53:49 UTC  
> Url: https://github.com/boostorg/container/issues/180  

The following fails:  
  
```c++  
#include <boost/asio/execution/allocator.hpp>  
#include <boost/asio/query.hpp>  
#include <boost/asio/require.hpp>  
#include <boost/asio/system_executor.hpp>  
#include <boost/container/pmr/polymorphic_allocator.hpp>  
  
using my_allocator_t = boost::container::pmr::polymorphic_allocator<char>;  
using executor_t = boost::asio::require_result<  
    boost::asio::system_executor,  
    boost::asio::execution::allocator_t<my_allocator_t>  
>::type;  
static_assert(std::is_same_v<  
    boost::asio::query_result<  
        executor_t,  
        boost::asio::execution::allocator_t<void>  
    >::type,  
    my_allocator_t  
>);  
```  
  
This happens because:  
- `query_result` checks `is_applicable_property_v<executor_t,allocator_t<void>>`,  
- `allocator_t<T>::is_applicable_property_v` checks `is_executor<executor_t>`,  
- which checks `is_nothrow_copy_constructible_v<executor_t>`, which fails,  
- because `basic_system_executor` has copy special member functions implicitly defaulted, which makes them `noexcept(false)`,  
- because `basic_system_executor` has a member of type `my_allocator_t` and  
- `boost::container::pmr::polymorphic_allocator<T>` has user-provided copy constructor/assignment functions that are not marked `noexcept`, defaulting to `noexcept(false)`.  
  
While many "Throws: Nothing." functions are not `noexcept` at the language level for various reasons, I believe ASIO has the right to check this property formally on special member functions. Copy constructor/assignment functions in `boost::container::polymorphic_allocator` should either be marked `BOOST_NOEXCEPT` or removed altogether (which would also make them trivial). I don't know whether changing their triviality could be a problem and where their docs should go if they were removed.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-04-20 22:54:03 UTC  
> Url: https://github.com/boostorg/container/issues/180#issuecomment-823650666  

Many thanks for the report!
