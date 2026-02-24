# #759 - x3::variant's converting constructor is explicit [Closed]

> Username: PhoenixCausesOof  
> Created at: 2023-03-30 13:08:07 UTC  
> Updated at: 2023-04-01 22:16:36 UTC  
> Closed at: 2023-04-01 22:16:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/759  

In contrast to the STL's variant implementation, Spirit's implementation has an explicit converting constructor:  
  
```cpp  
  
// STL  
template<class T>  
variant(T&& t) noexcept(...);  
  
// boost/spirit/home/x3/support/ast/variant.hpp  
template <typename T, class = non_self_t<T>>  
explicit variant(T const &rhs) BOOST_NOEXCEPT_IF(  
    (std::is_nothrow_constructible<variant_type, T const &>::value))  
    : var(rhs) {}  
  
template <typename T, class = non_self_t<T>>  
explicit variant(T &&rhs) BOOST_NOEXCEPT_IF(  
    (std::is_nothrow_constructible<variant_type, T &&>::value))  
    : var(std::forward<T>(rhs)) {}  
```  
  
AFAIK, that means this constructor will never actually be used.

---

## Comment 1

> Username: Kojoley  
> Created at: 2023-03-30 17:17:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/759#issuecomment-1490656288  

> In contrast to the STL's variant implementation, Spirit's implementation has an explicit converting constructor:  
  
See https://github.com/boostorg/spirit/pull/380, people want an explicit constructor.  
  
> AFAIK, that means this constructor will never actually be used.  
  
```cpp  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
  
int main()  
{  
    boost::spirit::x3::variant<int>(0);  
    boost::spirit::x3::variant<int>((0));  
}  
```  
  
Would not compile then, I also stepped into the debugger to check.  
  
But If your 'this' means the `variant(T const &rhs)` - you are right that it will be never used, not because of `explicit` but because `variant(T &&rhs)` is preferred by overload resolution due to forwarding reference.
