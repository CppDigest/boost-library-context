# #530 - hana::less does not work for adapted structs [Open]

> Username: PiotrNycz  
> Created at: 2024-05-23 06:52:18 UTC  
> Updated at: 2024-05-24 16:30:39 UTC  
> Url: https://github.com/boostorg/hana/issues/530  

Consider this code [godbolt](https://godbolt.org/z/34jT7r7MG)  
The codebase is c++17 - so I cannot just default compare operators...  
  
For any adapted struct (in hana (actually boost::hana) way) I have this strange situation when `hana::equal` just works for implementing `operator==` while `hana::less` cannot be used for implementing `operator<`. For me documentation is unclear - should it work?  
  
I dig a little into this problem - and it seems that when I call directly `hana::less` from `operator<` then `less` calls `operator<` so infinite recursion - so segfault.  
While when it is called directly (not via operator<) then it does not compile with a set of static-assert, starting from complaining about adapted struct being not ordered - well...  
  
When I compared sources for `equal` and `less` - indeed - support for adapted structs is missing in `less`.  
  
The code:  
  
```  
#include <boost/hana/adapt_struct.hpp>  
#include <boost/hana/equal.hpp>  
#include <boost/hana/less.hpp>  
  
namespace demo  
{  
struct Some  
{  
    int a;  
    int b;  
};  
}  
  
BOOST_HANA_ADAPT_STRUCT(demo::Some,  
    a, b);  
  
namespace demo  
{  
bool operator==(const Some& lhs, const Some& rhs)  
{  
    return boost::hana::equal(lhs, rhs);  
}  
  
  
bool less(const Some& lhs, const Some& rhs)  
{  
    return boost::hana::less(lhs, rhs);  
}  
  
bool operator<(const Some& lhs, const Some& rhs)  
{  
    // uncommenting boost::hana::less makes this segfault - because operator< calls itself  
    //return boost::hana::less(lhs, rhs);  
    return less(lhs, rhs);  
}  
}  
  
int main() {  
    return demo::Some{1, 1} < demo::Some{1, 2};  
}  
```  
  
The output:  
  
```  
In file included from <source>:3:  
/opt/compiler-explorer/libs/boost_1_84_0/boost/hana/less.hpp: In instantiation of 'constexpr auto boost::hana::less_t::operator()(X&&, Y&&) const [with X = const demo::Some&; Y = const demo::Some&]':  
<source>:29:29:   required from here  
/opt/compiler-explorer/libs/boost_1_84_0/boost/hana/less.hpp:51:43: error: static assertion failed: hana::less(x, y) requires 'x' to be Orderable  
   51 |         static_assert(hana::Orderable<T>::value,  
      |                                           ^~~~~  
/opt/compiler-explorer/libs/boost_1_84_0/boost/hana/less.hpp:51:43: note: 'std::integral_constant<bool, false>::value' evaluates to false  
/opt/compiler-explorer/libs/boost_1_84_0/boost/hana/less.hpp:54:43: error: static assertion failed: hana::less(x, y) requires 'y' to be Orderable  
   54 |         static_assert(hana::Orderable<U>::value,  
      |                                           ^~~~~  
/opt/compiler-explorer/libs/boost_1_84_0/boost/hana/less.hpp:54:43: note: 'std::integral_constant<bool, false>::value' evaluates to false  
/opt/compiler-explorer/libs/boost_1_84_0/boost/hana/less.hpp:57:53: error: static assertion failed: hana::less(x, y) requires 'x' and 'y' to be embeddable in a common Orderable  
   57 |         static_assert(!is_default<less_impl<T, U>>::value,  
      |                                                     ^~~~~  
/opt/compiler-explorer/libs/boost_1_84_0/boost/hana/less.hpp:57:53: note: '!(bool)std::integral_constant<bool, true>::value' evaluates to false  
/opt/compiler-explorer/libs/boost_1_84_0/boost/hana/less.hpp:62:27: error: use of deleted function 'static constexpr auto boost::hana::deleted_implementation::apply(T&& ...) [with T = {const demo::Some&, const demo::Some&}]'  
   62 |         return Less::apply(static_cast<X&&>(x), static_cast<Y&&>(y));  
      |                ~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /opt/compiler-explorer/libs/boost_1_84_0/boost/hana/core/dispatch.hpp:14,  
                 from /opt/compiler-explorer/libs/boost_1_84_0/boost/hana/value.hpp:18,  
                 from /opt/compiler-explorer/libs/boost_1_84_0/boost/hana/concept/constant.hpp:19,  
                 from /opt/compiler-explorer/libs/boost_1_84_0/boost/hana/core/to.hpp:15,  
                 from /opt/compiler-explorer/libs/boost_1_84_0/boost/hana/bool.hpp:17,  
                 from /opt/compiler-explorer/libs/boost_1_84_0/boost/hana/string.hpp:15,  
                 from /opt/compiler-explorer/libs/boost_1_84_0/boost/hana/detail/struct_macros.hpp:28,  
                 from /opt/compiler-explorer/libs/boost_1_84_0/boost/hana/adapt_struct.hpp:15,  
                 from <source>:1:  
/opt/compiler-explorer/libs/boost_1_84_0/boost/hana/detail/dispatch_if.hpp:21:31: note: declared here  
   21 |         static constexpr auto apply(T&& ...) = delete;  
      |                               ^~~~~  
Compiler returned: 1  
  
```  
  
P.S.  
I tried also to use hana::lexicographical_compare` - but I did not manage to use it for adapted structs - any help would be welcome.  
I ended up with using `hana::all_of`...

---

## Comment 1

> Username: ricejasonf  
> Created at: 2024-05-23 17:01:00 UTC  
> Updated at: 2024-05-23 21:50:04 UTC  
> Url: https://github.com/boostorg/hana/issues/530#issuecomment-2127648404  

The compiler error is that your type `Some` does not satisfy the concept `hana::Orderable` which requires an implementation of `hana::less` for your type. So, I am not seeing how your commented call to `boost::hana::less` would segfault if it would not compile.  
  
In any case, you would need to implement `hana::less`. Unfortunately a `struct` cannot implement `hana::Iterable` to get the `lexicographical_compare` function to make this easy, but here is a example that skirts around that by converting `Some` to a `tuple` in the implementation.  
[https://godbolt.org/z/f5YK4j3ov](https://godbolt.org/z/f5YK4j3ov)  
  
EDIT: For the documentation, see `Reference documentation -> Concepts -> Struct` to see the "refined concepts". Notice that `Orderable` is not there.  
  
EDIT: BTW The `lexicographical_compare` requires `Iterable` which requires a definition for `drop_front`. Hence, implementing it for `Structs` in general is not straightforward.

---

## Comment 2

> Username: PiotrNycz  
> Created at: 2024-05-24 06:02:28 UTC  
> Updated at: 2024-05-24 06:11:18 UTC  
> Url: https://github.com/boostorg/hana/issues/530#issuecomment-2128606878  

1) SEGFAULT explanation:  
  
The segfault is when calling `hana::less` from `operator<` -- then, as I wrote, `hana::less` calls `operator<` which calls `hana::less` - so infinite recursion:  
  
```  
#include <boost/hana/adapt_struct.hpp>  
#include <boost/hana/equal.hpp>  
#include <boost/hana/less.hpp>  
  
namespace demo  
{  
struct Some  
{  
    int a;  
    int b;  
};  
}  
  
BOOST_HANA_ADAPT_STRUCT(demo::Some,  
    a, b);  
  
namespace demo  
{  
bool operator==(const Some& lhs, const Some& rhs)  
{  
    return boost::hana::equal(lhs, rhs);  
}  
  
bool operator<(const Some& lhs, const Some& rhs)  
{  
    // uncommenting boost::hana::less makes this segfault - because operator< calls itself  
    return boost::hana::less(lhs, rhs);  
}  
}  
  
int main() {  
    return demo::Some{1, 1} < demo::Some{1, 2};  
}  
  
```  
  
2) Well...  
  
So, in general, I need to implement hana::less_impl to use hana::less for every struct (hundreds of them I have) in my project, to use hana::less to implement `operator<` for my structs. Well, instead I can just implement my operator< with std::tie, can't I - and just forget about hana library?  
  
My goal is to use `BOOST_HANA_ADAPT_STRUCT(demo::Some,  a, b, ... tens of fields here...);` when implementing operator<. I did not want to repeat these fields agains in operator< -- because then - these ADAPT_STRUCT is useless.  
The other thing is that I do not want to copy all fields into the hana tuple -- I know, I can use std::reference_wrapper - or just use `std::tie`.  
  
Just for information - I did this to have implementation of operator< and still use ADAPT_STRUCT:  
  
```  
template <typename T> // assuming T has hana::accessors  
int compare(const Some& lhs, const Some& rhs) // it behaves as C++20 operator<=>  
{  
//    return boost::hana::less(lhs, rhs);  
    int result{};  
    static_cast<void>(boost::hana::all_of(boost::hana::accessors<T>(),  
        [&](const auto& field)  
        {  
            if (result != 0) return false;  
            auto get = boost::hana::second(field);  
            auto&& lhs_field = get(lhs);  
            auto&& rhs_field = get(rhs);  
            if (lhs_field < rhs_field) { result = -1; return false; }  
            if (rhs_field < lhs_field) { result = 1; return false; }  
            return true;  
        }));  
    return result;  
}  
  
bool operator<(const Some& lhs, const Some& rhs)  
{  
    return compare(lhs, rhs) < 0;  
}  
  
  
```  
  
3)  
For me, it is hard to believe that adding support for adapted structs in hana::less is so hard, it was done for hana::equal and it just works. But ok, for C++20 it is not needed...

---

## Comment 3

> Username: ricejasonf  
> Created at: 2024-05-24 16:30:38 UTC  
> Url: https://github.com/boostorg/hana/issues/530#issuecomment-2129950476  

> So, in general, I need to implement hana::less_impl to use hana::less for every struct (hundreds of them I have) in my project, to use hana::less to implement operator< for my structs. Well, instead I can just implement my operator< with std::tie, can't I - and just forget about hana library?  
  
The only benefits to implementing `hana::less` that I see immediately are that you get `hana::greater`, `hana::min`, and `hana::max` for free and your data type is then `hana::Orderable`. Beyond that I am not sure there is any other benefit.  
  
In general, adapted structs are not considered ordered because it is the user who should opt in and define how that ordering should work. I made a more general solution that uses `hana::accessors` to fix the issue with copying and allows us to use the `hana::lexicographical_compare` function with a predicate. To opt in, I made a "concept" to indicate the struct is to be ordered lexicographically. (I did not test this thoroughly.)  
  
```cpp  
namespace boost::hana {  
  template <typename T>  
  struct less_impl<T, T,  
            hana::when<demo::concept::LexicographicallyOrderedStruct<T>::value>> {  
    static constexpr auto apply(demo::Some const& x,  
                                demo::Some const& y) {  
      constexpr auto accessors = hana::transform(hana::accessors<T>(),  
                                                 hana::second);  
      return hana::lexicographical_compare(accessors, accessors,  
        [&](auto accessor, auto) {  
          return hana::less(accessor(x), accessor(y));  
        });  
    }  
  };  
}  
  
```  
  
[https://godbolt.org/z/sbrPzMf3a](https://godbolt.org/z/sbrPzMf3a)
