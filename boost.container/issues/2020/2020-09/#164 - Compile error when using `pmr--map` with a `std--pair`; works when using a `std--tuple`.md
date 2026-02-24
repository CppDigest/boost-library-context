# #164 - Compile error when using `pmr::map` with a `std::pair`; works when using a `std::tuple` [Closed]

> Username: seelabs  
> Created at: 2020-09-15 14:25:22 UTC  
> Updated at: 2021-01-10 17:23:52 UTC  
> Closed at: 2020-11-13 22:43:10 UTC  
> Url: https://github.com/boostorg/container/issues/164  

Given the following program:  
```  
#include <boost/container/pmr/map.hpp>  
#include <boost/container/pmr/monotonic_buffer_resource.hpp>  
  
#include <memory>  
  
int  
main()  
{  
    // tuple works  
    // using test_t = boost::container::pmr::map<int, std::tuple<int, int>>;  
    // pair does not work  
    using test_t = boost::container::pmr::map<int, std::pair<int, int>>;  
    boost::container::pmr::monotonic_buffer_resource org_buf;  
    test_t org{&org_buf};  
    boost::container::pmr::monotonic_buffer_resource copy_buf;  
    test_t copy{org, &copy_buf};  
  
    return 0;  
}  
```  
Results in the following compile error:  
```  
/celibs/boost_1_74_0/boost/container/detail/dispatch_uses_allocator.hpp:251:8: error: call to function 'dispatch_uses_allocator' that is neither visible in the template definition nor found by argument-dependent lookup  
  
      (dispatch_uses_allocator)(construct_alloc, arg_alloc, dtl::addressof(p->second), ::boost::forward<V>(y));  
```  
However, if `test_t` is redefined to use a `std::tuple` instead of a `std::pair` the program compiles. This is unexpected, as a `std::pair` and a two element `std::tuple` should act similarly.  
  
Tested with boost 1.74 and both clang 10 and gcc 10. Also, if I write a similar test program with `std::pmr` instead of `boost::pmr` both the `pair` and `tuple` versions work.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-11-13 22:43:22 UTC  
> Url: https://github.com/boostorg/container/issues/164#issuecomment-727071035  

Thanks for the report!

---

## Comment 2

> Username: mrks  
> Created at: 2021-01-10 17:23:52 UTC  
> Url: https://github.com/boostorg/container/issues/164#issuecomment-757511924  

Just ran into the same issue. Thank you @seelabs for reporting it and @igaztanaga for both fixing it and documenting the `tuple` workaround!
