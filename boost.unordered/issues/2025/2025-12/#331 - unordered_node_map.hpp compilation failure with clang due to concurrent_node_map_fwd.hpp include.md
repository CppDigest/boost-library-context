# #331 - unordered_node_map.hpp compilation failure with clang due to concurrent_node_map_fwd.hpp include [Open]

> Username: indiosmo  
> Created at: 2025-12-30 18:54:36 UTC  
> Updated at: 2025-12-31 20:06:55 UTC  
> Url: https://github.com/boostorg/unordered/issues/331  

When returning an `unordered_node_map` in a `boost::leaf::result` from a function, clang won't compile due to incomplete type related to concurrent_node_map.  
  
Also applies to `unordered_flat_map`.  
  
Please let me know if this should be filed in the leaf repo instead.  
  
Building with clang 22 using `--gcc-toolchain=<path-to-gcc-15.2.0>`   
  
Reproducible Example  
```cpp  
#include "boost/unordered/unordered_node_map.hpp"  
#include "boost/leaf/result.hpp"  
  
template <typename T>  
boost::leaf::result<T> make_result_error()  
{  
  return boost::leaf::new_error();  
}  
  
int main()  
{  
  using map_t = boost::unordered::unordered_node_map<std::string, std::string>;  
  
  auto result = make_result_error<map_t>();  
  
  return 0;  
}  
```  
  
Error  
```  
auto result = make_result_error<map_t>();  
.../boost_1_90_0/include/boost/unordered/concurrent_node_map_fwd.hpp:32:11: note: template is declared here  
   class concurrent_node_map;  
In file included from .../boost_1_90_0/include/boost/unordered/unordered_node_map.hpp:14:  
In file included from .../boost_1_90_0/include/boost/unordered/concurrent_node_map_fwd.hpp:19:  
In file included from .../gcc-15.2.0/lib/gcc/x86_64-pc-linux-gnu/15.2.0/../../../../include/c++/15.2.0/functional:51:  
In file included from .../gcc-15.2.0/lib/gcc/x86_64-pc-linux-gnu/15.2.0/../../../../include/c++/15.2.0/bits/stl_function.h:60:  
In file included from .../gcc-15.2.0/lib/gcc/x86_64-pc-linux-gnu/15.2.0/../../../../include/c++/15.2.0/bits/move.h:37:  
.../gcc-15.2.0/lib/gcc/x86_64-pc-linux-gnu/15.2.0/../../../../include/c++/15.2.0/type_traits:1159:21: error: static assertion failed due to requirement 'std::__is_complete_or_unbounded(std::__type_identity<boost::unordered::concurrent_node_map<std::basic_string<char, std::char_traits<char>, std::allocator<char>>, std::basic_string<char, std::char_traits<char>, std::allocator<char>>, boost::hash<std::basic_string<char, std::char_traits<char>, std::allocator<char>>>, std::equal_to<std::basic_string<char, std::char_traits<char>, std::allocator<char>>>, std::allocator<std::pair<const std::basic_string<char, std::char_traits<char>, std::allocator<char>>, std::basic_string<char, std::char_traits<char>, std::allocator<char>>>>>>{})': template argument must be a complete class or an unbounded array  
static_assert(std::__is_complete_or_unbounded(__type_identity<_Tp>{}),  
```

---

## Comment 1

> Username: k3DW  
> Created at: 2025-12-30 23:11:56 UTC  
> Updated at: 2025-12-30 23:12:11 UTC  
> Url: https://github.com/boostorg/unordered/issues/331#issuecomment-3700789994  

This looks like it's happening because of [`boost::leaf::error_id`'s templated conversion operator](https://github.com/boostorg/leaf/blob/4c8c5ec1aad6e9300e5a5941a45d4fd60b05227c/include/boost/leaf/error.hpp#L733-L737).  
  
I haven't found a solution, but here's a reduced repro of the issue without Boost.Leaf.  
```cpp  
#include "boost/unordered/unordered_node_map.hpp"  
  
struct S  
{  
  template <  
    class T,  
    typename std::enable_if<std::is_constructible<T, int>::value, int>::type = 0>  
  operator T() const noexcept;  
};  
  
int main()  
{  
  using map_t = boost::unordered::unordered_node_map<int, int>;  
  
  (void)std::is_constructible<map_t, S>::value;  
}  
```  
  
Note that `boost::unordered_node_map` has a constructor that looks [like this](https://github.com/boostorg/unordered/blob/3dde65b2ef022fa60c274d16f86722010d71c992/include/boost/unordered/unordered_node_map.hpp#L192-L197)  
```cpp  
template <bool avoid_explicit_instantiation = true>  
unordered_node_map(  
  concurrent_node_map<Key, T, Hash, KeyEqual, Allocator>&& other)  
    : table_(std::move(other.table_))  
{  
}  
```  
  
I think the implicit templated conversion operator is triggering an instantiation of `concurrent_node_map` through this constructor. I'm not sure how to fix it yet.

---

## Comment 2

> Username: k3DW  
> Created at: 2025-12-30 23:36:29 UTC  
> Url: https://github.com/boostorg/unordered/issues/331#issuecomment-3700855356  

As a workaround for now, you can include `"boost/unordered/concurrent_node_map.hpp"`, and everything will work as it should.

---

## Comment 3

> Username: indiosmo  
> Created at: 2025-12-31 00:31:50 UTC  
> Updated at: 2025-12-31 00:32:46 UTC  
> Url: https://github.com/boostorg/unordered/issues/331#issuecomment-3700923505  

> As a workaround for now, you can include `"boost/unordered/concurrent_node_map.hpp"`, and everything will work as it should.  
  
Yep, that's what I ended up doing, just thought it would be good to report the issue.  
  
Thanks.

---

## Comment 4

> Username: cmazakas  
> Created at: 2025-12-31 16:21:57 UTC  
> Url: https://github.com/boostorg/unordered/issues/331#issuecomment-3702465757  

An approach like this seems to work: https://godbolt.org/z/98jb9nhch  
  
```cpp  
#include <type_traits>  
#include <system_error>  
  
template<class Key, class T>  
struct concurrent_map;  
  
struct S  
{  
  template <  
    class T,  
    typename std::enable_if<std::is_constructible<T, int>::value, int>::type = 0>  
  operator T() const noexcept  
  {  
    return {};  
  }  
};  
  
template<class Key, class T>  
struct map   
{  
    map() {}  
    // template<bool = false>  
    // map(concurrent_map<Key, T> other) {  
    //     other.x;  
    // }  
      
    template<class U = T>  
    map(concurrent_map<Key, U> other) {  
        static_assert(std::is_same_v<T, U>);  
        other.x;  
    }  
};  
  
void f();  
  
int main()  
{  
    static_assert(!std::is_constructible_v<map<int, int>, S>);  
}  
  
template<class Key, class T>  
struct concurrent_map  
{  
    int x = 1234;  
};  
  
void f()  
{  
    map<int, int> m(concurrent_map<int, int>{});  
}  
```

---

## Comment 5

> Username: k3DW  
> Created at: 2025-12-31 17:14:37 UTC  
> Updated at: 2025-12-31 17:17:51 UTC  
> Url: https://github.com/boostorg/unordered/issues/331#issuecomment-3702542390  

That approach breaks overload resolution. Because the constructor argument now depends on a template argument, we can't pass the `map` anything that actually does have a conversion to `concurrent_map`, whereas we could do that before. Adding the code below to your example doesn't compile, although it would compile if we keep the `map` constructor the same as before.  
  
```cpp  
struct Wrapper  
{  
    operator concurrent_map<int, int>() const  
    {  
        return {};  
    }  
};  
map<int, int> m2(Wrapper{});  
```  
  
I think I have a set of constructors that replicates the same overload resolution behaviour, but fixes the error seen in this issue. I've tried it in Compiler Explorer [here](https://godbolt.org/z/WMEfeYoao), but I haven't yet tested it in the Unordered code. It's much more complicated, but it seems to work.  
  
```cpp  
    template <  
        class ConcurrentMap,  
        typename std::enable_if<  
            // Ensure we match exactly `concurrent_map<Key, T>&&`.  
            // Any lvalue references to `concurrent_map<Key, T>` are not supported.  
            std::is_same<ConcurrentMap, concurrent_map<Key, T>>::value  
        , int>::type = 0>  
    map(ConcurrentMap&& other) {  
        (void)other.x;  
    }  
      
    template <  
        class U,  
        typename std::enable_if<  
            // Ensure we don't match any cvref-qualified `concurrent_map<Key, T>&&`,  
            !is_similar<U, concurrent_map<Key, T>>::value  
            // but we do match anything convertible to `concurrent_map<Key, T>`.  
            && std::is_convertible<U&&, concurrent_map<Key, T>>::value  
        , int>::type = 0>  
    map(U&& other)  
        : map(concurrent_map<Key, T>(static_cast<U&&>(other)))  
    {  
    }  
```  
  
Since the original constructor is on `concurrent_map&&`, that means it can match an rvalue reference to `concurrent_map` itself, as well as anything that can implicitly convert to `concurrent_map`. In this templated case, we need to separate those possibilities out into 2 different constructors.  
  
I think the key here is SFINAE-ing on `is_convertible` but not `is_constructible`. Changing that one condition to `is_constructible` would trigger the issue again.  
  
I'll try this in the library itself.

---

## Comment 6

> Username: cmazakas  
> Created at: 2025-12-31 18:37:18 UTC  
> Updated at: 2025-12-31 18:37:39 UTC  
> Url: https://github.com/boostorg/unordered/issues/331#issuecomment-3702683596  

Sure; it was just an idea. The point was that we needed to move away from the NTTP approach. I was just trying to help.

---

## Comment 7

> Username: k3DW  
> Created at: 2025-12-31 20:06:55 UTC  
> Url: https://github.com/boostorg/unordered/issues/331#issuecomment-3702835421  

Yeah thank you, I couldn't see how to do it until seeing your idea
