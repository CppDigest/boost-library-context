# #60 - reference to an addressof function is ambigous becase deletion in c++11 [Closed]

> Username: BuzaL  
> Created at: 2019-06-12 20:17:24 UTC  
> Updated at: 2019-06-14 00:21:06 UTC  
> Closed at: 2019-06-13 01:34:51 UTC  
> Url: https://github.com/boostorg/core/issues/60  

In 1.65.0 were introduce the deletion of const T* addressof(T&&) to get a readable error message if you call it with rvalue.  
  
The problem is all the compilers fail to compile this:  
`std::function<int*(int&)> f = boost::addressof<int>;`  
  
> source>:21:27: error: no viable conversion from '<overloaded function type>' to std::function<int *(int &)>'  
  
OR this  
`auto g = boost::addressof<int>;`  
  
> prog.cc:36:17: error: unable to deduce 'auto' from 'addressof<int>'  
  
Thus you **can't use in boost::transform** either.  
Removing the =delete section solve the issue, but you got back the ugly error-message.  
  
We have tried to handle with enable_if/is_rvalue_reference, but we!ve failed.  
The only workaround for this situation is static_cast, ie.:  
```  
struct my_long_struct;  
auto my_address_of = static_cast<my_long_struct*(&)(my_long_struct&)>(boost::addressof<my_long_struct>);  
boost::transform( ..., my_address_of );  
```  
  
  
or, maybe could be extend with addressof_ptr() function, which provider the corresponding function reference, like this:  
```  
namespace detail {  
template<class T>  
using addr_func = T* (&) (T&);  
}  
  
template<class T>  
BOOST_CONSTEXPR detail::addr_func<T>  
addressof_ptr() BOOST_NOEXCEPT  
{  
    return static_cast<T*(&)(T&)>(boost::addressof<T>);  
}  
// Usage:  
auto f = boost::addressof_ptr<int>();  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2019-06-12 23:29:28 UTC  
> Url: https://github.com/boostorg/core/issues/60#issuecomment-501491144  

> We have tried to handle with enable_if/is_rvalue_reference, but we've failed.  
> The only workaround for this situation is static_cast,  
  
Why not:  
```  
template<class T>  
inline T* address(T& v) noexcept  
{  
    return boost::addressof(v);  
}  
```  
For example:  
```  
#include <boost/core/addressof.hpp>  
#include <functional>  
  
template<class T>  
inline T* address(T& v) noexcept  
{  
    return boost::addressof(v);  
}  
  
int main()  
{  
    std::function<int*(int&)> f = address<int>;  
    auto g = address<int>;  
    int i;  
    return !(f(i) == g(i));  
}  
```

---

## Comment 2

> Username: Lastique  
> Created at: 2019-06-13 01:33:19 UTC  
> Updated at: 2019-06-13 01:34:26 UTC  
> Url: https://github.com/boostorg/core/issues/60#issuecomment-501514295  

Given that any foreign function, including `boost::addressof` and `std::addressof`, can be overloaded, taking its address is always fragile and is not a generally supported usage pattern. You should really use a proxy function, function object or lambda, similar to what @glenfe suggested. Otherwise you must use a cast, but then you must hardcode the exact signature and template parameters of the function into your code, which will likely break at some point and therefore is not a good practice either. The `addressof_ptr` approach simply doesn't scale.

---

## Comment 3

> Username: BuzaL  
> Created at: 2019-06-13 17:51:55 UTC  
> Url: https://github.com/boostorg/core/issues/60#issuecomment-501811685  

Thank you for the answers, we've did the proxy first (what @glenfe suggested too), but do we (all of us) really need to code a proxy for a proxy, instead of giving a common solution for everyone?  
  
> We have tried to handle with enable_if/is_rvalue_reference  
I meant to solve the =delete issue, we fixed the issue, but we can't earn to generate "using a delete function" error with it for bad cases.

---

## Comment 4

> Username: Lastique  
> Created at: 2019-06-14 00:21:05 UTC  
> Url: https://github.com/boostorg/core/issues/60#issuecomment-501923941  

I don't think we want to guarantee a single overload of any public functions. And providing a proxy for every public function is unreasonable. So yes, if you need to take an address of a function, make that your own function that you have control of. This is not a very common need anyway.
