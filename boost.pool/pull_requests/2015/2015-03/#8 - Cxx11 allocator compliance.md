# #8 Cxx11 allocator compliance [Merged]

> Username: mfontanini  
> Created at: 2015-03-17 04:37:54 UTC  
> Updated at: 2016-12-24 13:54:59 UTC  
> Merged at: 2016-12-24 13:54:59 UTC  
> Closed at: 2016-12-24 13:54:59 UTC  
> Url: https://github.com/boostorg/pool/pull/8  

`pool_allocator` and `fast_pool_allocator` are not compliant to the C++11 allocator interface. Besides some methods being `static` when they should be instance methods and `const` qualified (also `noexcept` qualified on C++11), `(fast_)pool_allocator::construct` should take a variadic template parameter pack rather than just a `const value_type&`, which makes it fail to compile when using it with containers such as libstdc++'s `unordered_map`.   
  
This patch detects whether variadic templates are supported and defines the `construct` method using the appropriate signature. This should be backwards compatible.  
  
I also wanted to change the signature of the currently static methods methods, but I guess that might break some code that's relying on them being static.  
  
This is a sample code that wouldn't compile without this patch:  
  
``` c++  
#include <boost/pool/pool_alloc.hpp>  
#include <unordered_map>  
  
using map_type = std::unordered_map<  
    int,  
    int,  
    std::hash<int>,  
    std::equal_to<int>,  
    boost::fast_pool_allocator<std::pair<int, int>>  
>;  
  
int main() {  
    map_type mt;  
    mt[42] = 42;  
}  
```

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2015-03-17 06:48:08 UTC  
> Url: https://github.com/boostorg/pool/pull/8#issuecomment-82157463  

BOOST_NO_CXX11_RVALUE_REFERENCES should be checked too for rvalue references and std::forward. Otherwise, this breaks support for C++11 compilers with C++03 standard libraries, e.g. clang with libstdc++ on OS X (which is not officially supported, but currently works).

---

## Comment 2

> Username: mfontanini  
> Created_at: 2015-03-17 07:18:39 UTC  
> Url: https://github.com/boostorg/pool/pull/8#issuecomment-82174081  

You are right. I added a check for that macro too.

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2015-03-17 08:03:28 UTC  
> Url: https://github.com/boostorg/pool/pull/8#issuecomment-82193069  

Thanks!

---

## Comment 4

> Username: mfontanini  
> Created_at: 2015-09-03 22:37:04 UTC  
> Url: https://github.com/boostorg/pool/pull/8#issuecomment-137592047  

Is this going to be merged? This is a pretty good library and it kind of sucks that you can't use it with the new containers on C++11.

---

## Comment 5

> Username: pepsiman  
> Created_at: 2016-06-13 14:03:59 UTC  
> Url: https://github.com/boostorg/pool/pull/8#issuecomment-225590315  

See also https://svn.boost.org/trac/boost/ticket/10897

---
