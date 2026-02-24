# #110 - tuple_size_v fails with optional<unique_ptr<...>> field in the structure [Closed]

> Username: dragon-dreamer  
> Created at: 2022-11-30 00:31:11 UTC  
> Updated at: 2025-09-14 12:03:02 UTC  
> Closed at: 2025-09-14 12:03:02 UTC  
> Url: https://github.com/boostorg/pfr/issues/110  

This code fails to compile with both clang and gcc trunk (but compiles with the latest MSVC):  
```cpp  
#include <memory>  
#include <optional>  
  
#include <boost/pfr/core.hpp>  
  
struct optional_linked_list  
{  
	int value;  
	std::optional<std::unique_ptr<optional_linked_list>> next;  
};  
  
int main()  
{  
    return boost::pfr::tuple_size_v<optional_linked_list>;  
}  
```  
Also see https://godbolt.org/z/1d9W5e4b3 (tested with Boost 1.80.0).  
  
This is the clang-trunk compile error:  
```  
In file included from <source>:4:  
In file included from /opt/compiler-explorer/libs/boost_1_80_0/boost/pfr/core.hpp:12:  
In file included from /opt/compiler-explorer/libs/boost_1_80_0/boost/pfr/detail/core.hpp:17:  
In file included from /opt/compiler-explorer/libs/boost_1_80_0/boost/pfr/detail/core17.hpp:11:  
In file included from /opt/compiler-explorer/libs/boost_1_80_0/boost/pfr/detail/fields_count.hpp:13:  
/opt/compiler-explorer/libs/boost_1_80_0/boost/pfr/detail/unsafe_declval.hpp:29:12: error: static_cast from 'typename std::remove_reference<unique_ptr<optional_linked_list>>::type' (aka 'std::unique_ptr<optional_linked_list>') to 'std::unique_ptr<optional_linked_list>' uses deleted function  
    return static_cast<T>(*ptr);  
           ^~~~~~~~~~~~~~~~~~~~  
/opt/compiler-explorer/libs/boost_1_80_0/boost/pfr/detail/fields_count.hpp:45:24: note: in instantiation of function template specialization 'boost::pfr::detail::unsafe_declval<std::unique_ptr<optional_linked_list>>' requested here  
        return detail::unsafe_declval<Type>();  
                       ^  
/opt/compiler-explorer/gcc-snapshot/lib/gcc/x86_64-linux-gnu/13.0.0/../../../../include/c++/13.0.0/optional:244:17: note: in instantiation of function template specialization 'boost::pfr::detail::ubiq_rref_constructor::operator unique_ptr<std::unique_ptr<optional_linked_list>>' requested here  
            : _M_value(std::forward<_Args>(__args)...)  
                       ^  
/opt/compiler-explorer/gcc-snapshot/lib/gcc/x86_64-linux-gnu/13.0.0/../../../../include/c++/13.0.0/bits/unique_ptr.h:523:7: note: candidate constructor has been explicitly deleted  
      unique_ptr(const unique_ptr&) = delete;  
      ^  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-09-13 16:03:28 UTC  
> Url: https://github.com/boostorg/pfr/issues/110#issuecomment-3288576109  

Looks like the new unsafe_declval fixes this issue. Will add tests in https://github.com/boostorg/pfr/pull/222
