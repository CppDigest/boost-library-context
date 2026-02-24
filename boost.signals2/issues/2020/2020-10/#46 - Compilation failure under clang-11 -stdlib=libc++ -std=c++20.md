# #46 - Compilation failure under clang-11 -stdlib=libc++ -std=c++20 [Closed]

> Username: madmongo1  
> Created at: 2020-10-31 07:15:43 UTC  
> Updated at: 2020-11-01 16:19:40 UTC  
> Closed at: 2020-11-01 16:19:40 UTC  
> Url: https://github.com/boostorg/signals2/issues/46  

Test program:  
  
```  
#include <boost/signals2.hpp>  
int main()  
{  
}  
```  
  
`clang-11 -stdlib=libc++ -std=c++20 test.cpp`  
  
I apprears that the code relies on the presence of std::allocator<void>::difference_type etc [which were removed in c++20 ?]  
  
Error output:  
```  
In file included from <source>:1:  
  
In file included from /celibs/boost_1_74_0/boost/signals2.hpp:19:  
  
In file included from /celibs/boost_1_74_0/boost/signals2/signal.hpp:22:  
  
In file included from /celibs/boost_1_74_0/boost/signals2/connection.hpp:21:  
  
/celibs/boost_1_74_0/boost/signals2/detail/auto_buffer.hpp:143:37: error: no type named 'size_type' in 'std::__1::allocator<boost::shared_ptr<void>>'  
  
        typedef typename Allocator::size_type            size_type;  
  
                ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~  
  
/celibs/boost_1_74_0/boost/signals2/connection.hpp:53:61: note: in instantiation of template class 'boost::signals2::detail::auto_buffer<boost::shared_ptr<void>, boost::signals2::detail::store_n_objects<10>, boost::signals2::detail::default_grow_policy, std::__1::allocator<boost::shared_ptr<void>>>' requested here  
  
        auto_buffer<shared_ptr<void>, store_n_objects<10> > garbage;  
  
                                                            ^  
  
In file included from <source>:1:  
  
In file included from /celibs/boost_1_74_0/boost/signals2.hpp:19:  
  
In file included from /celibs/boost_1_74_0/boost/signals2/signal.hpp:22:  
  
In file included from /celibs/boost_1_74_0/boost/signals2/connection.hpp:21:  
  
/celibs/boost_1_74_0/boost/signals2/detail/auto_buffer.hpp:144:37: error: no type named 'difference_type' in 'std::__1::allocator<boost::shared_ptr<void>>'  
  
        typedef typename Allocator::difference_type      difference_type;  
  
                ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~  
  
/celibs/boost_1_74_0/boost/signals2/detail/auto_buffer.hpp:143:37: error: no type named 'size_type' in 'std::__1::allocator<boost::variant<boost::shared_ptr<void>, boost::signals2::detail::foreign_void_shared_ptr>>'  
  
        typedef typename Allocator::size_type            size_type;  
  
                ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~  
  
/celibs/boost_1_74_0/boost/signals2/detail/slot_call_iterator.hpp:65:27: note: in instantiation of template class 'boost::signals2::detail::auto_buffer<boost::variant<boost::shared_ptr<void>, boost::signals2::detail::foreign_void_shared_ptr>, boost::signals2::detail::store_n_objects<10>, boost::signals2::detail::default_grow_policy, std::__1::allocator<boost::variant<boost::shared_ptr<void>, boost::signals2::detail::foreign_void_shared_ptr>>>' requested here  
  
        tracked_ptrs_type tracked_ptrs;  
  
                          ^  
  
In file included from <source>:1:  
  
In file included from /celibs/boost_1_74_0/boost/signals2.hpp:19:  
  
In file included from /celibs/boost_1_74_0/boost/signals2/signal.hpp:22:  
  
In file included from /celibs/boost_1_74_0/boost/signals2/connection.hpp:21:  
  
/celibs/boost_1_74_0/boost/signals2/detail/auto_buffer.hpp:144:37: error: no type named 'difference_type' in 'std::__1::allocator<boost::variant<boost::shared_ptr<void>, boost::signals2::detail::foreign_void_shared_ptr>>'  
  
        typedef typename Allocator::difference_type      difference_type;  
  
                ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~  
  
4 errors generated.  
  
Compiler returned: 1  
```  
  
Godbolt link: https://godbolt.org/z/cenzv1  
  
```  
$ clang++ --version  
clang version 11.0.0 (Fedora 11.0.0-1.fc33)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2020-11-01 16:19:40 UTC  
> Url: https://github.com/boostorg/signals2/issues/46#issuecomment-720113166  

Resolved in #47
