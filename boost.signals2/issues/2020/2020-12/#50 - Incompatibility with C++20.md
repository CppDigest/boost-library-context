# #50 - Incompatibility with C++20 [Closed]

> Username: BratSinot  
> Created at: 2020-12-02 07:25:48 UTC  
> Updated at: 2023-04-24 08:13:01 UTC  
> Closed at: 2020-12-02 07:36:55 UTC  
> Url: https://github.com/boostorg/signals2/issues/50  

In C++20 from `std::allocator` was removed some typedef's, and `boost/signals2.hpp` can't even compile:  
  
**test.cpp:**  
```  
#include <boost/signals2.hpp>  
  
auto main(void) -> int  
{  
    return 0;  
}  
  
```  
  
**Compiler output:**  
```  
➜  /tmp g++-10 -std=c++20 -Og test.cpp -o test  
In file included from /usr/include/boost/signals2/connection.hpp:21,  
                 from /usr/include/boost/signals2/signal.hpp:22,  
                 from /usr/include/boost/signals2.hpp:19,  
                 from test.cpp:1:  
/usr/include/boost/signals2/detail/auto_buffer.hpp: In instantiation of ‘class boost::signals2::detail::auto_buffer<boost::shared_ptr<void>, boost::signals2::detail::store_n_objects<10> >’:  
/usr/include/boost/signals2/connection.hpp:53:61:   required from here  
/usr/include/boost/signals2/detail/auto_buffer.hpp:145:58: error: no type named ‘pointer’ in ‘class std::allocator<boost::shared_ptr<void> >’  
  145 |         typedef typename Allocator::pointer              allocator_pointer;  
      |                                                          ^~~~~~~~~~~~~~~~~  
/usr/include/boost/signals2/detail/auto_buffer.hpp: In instantiation of ‘class boost::signals2::detail::auto_buffer<boost::variant<boost::shared_ptr<void>, boost::signals2::detail::foreign_void_shared_ptr>, boost::signals2::detail::store_n_objects<10> >’:  
/usr/include/boost/signals2/detail/slot_call_iterator.hpp:65:27:   required from here  
/usr/include/boost/signals2/detail/auto_buffer.hpp:145:58: error: no type named ‘pointer’ in ‘class std::allocator<boost::variant<boost::shared_ptr<void>, boost::signals2::detail::foreign_void_shared_ptr> >’  
```

---

## Comment 1

> Username: BratSinot  
> Created at: 2020-12-02 07:36:55 UTC  
> Url: https://github.com/boostorg/signals2/issues/50#issuecomment-737051803  

Sorry, missing: https://github.com/boostorg/signals2/commit/15fcf213563718d2378b6b83a1614680a4fa8cec

---

## Comment 2

> Username: yuqiii-wang  
> Created at: 2023-04-24 08:09:47 UTC  
> Url: https://github.com/boostorg/signals2/issues/50#issuecomment-1519592900  

or u can just try with c++14   
```  
# Set target C++ standard and required compiler features  
set(CMAKE_CXX_STANDARD 14)  
set(CMAKE_CXX_STANDARD_REQUIRED ON)  
```

---

## Comment 3

> Username: BratSinot  
> Created at: 2023-04-24 08:13:01 UTC  
> Url: https://github.com/boostorg/signals2/issues/50#issuecomment-1519598214  

> or u can just try with c++14  
>   
> ```  
> # Set target C++ standard and required compiler features  
> set(CMAKE_CXX_STANDARD 14)  
> set(CMAKE_CXX_STANDARD_REQUIRED ON)  
> ```  
  
To use C++20 I should switch to C++14? Thats very strange solution, don't you think? =)
