# #43 - `boost::swap` accepts const objects which causes run-time crashes [Closed]

> Username: akrzemi1  
> Created at: 2018-10-24 08:40:14 UTC  
> Updated at: 2018-10-24 13:55:54 UTC  
> Closed at: 2018-10-24 12:14:53 UTC  
> Url: https://github.com/boostorg/core/issues/43  

Consider the following code. This reproduces the situation from `boost::optional` (see [issue #63](https://github.com/boostorg/optional/issues/63)) which relies on `boost::swap`.  
  
```c++  
#include <boost/core/swap.hpp>  
  
namespace boost {  
    
template <typename T>  
struct Wrapper  
{  
  T value;  
};  
  
template <typename T>  
inline void swap (Wrapper<T> & w, Wrapper<T> & v)  
{  
  boost::swap(w, v);  
}  
    
} // namespace boost  
   
int main()  
{  
  const boost::Wrapper<int> w {2};  
  const boost::Wrapper<int> v {3};  
  swap(w, v); // segmentation fault  
}  
```  
  
The `swap` in the last line compiles, because the signature in `boost::swap` accepts references to const. I am not sure what happens next, but this is probably an infinite recursion.  
  
Note that `std::swap` does not have this problem, because it is constrained (T shall be move constructible and move assignable).

---

## Comment 1

> Username: pdimov  
> Created at: 2018-10-24 08:48:54 UTC  
> Url: https://github.com/boostorg/core/issues/43#issuecomment-432569727  

With what compiler does this compile?

---

## Comment 2

> Username: pdimov  
> Created at: 2018-10-24 08:53:18 UTC  
> Url: https://github.com/boostorg/core/issues/43#issuecomment-432571566  

Ah, I see. When `std::swap` is constrained.

---

## Comment 3

> Username: Lastique  
> Created at: 2018-10-24 12:28:36 UTC  
> Url: https://github.com/boostorg/core/issues/43#issuecomment-432634967  

We could probably use a compile-fail test. The one in the description works as one.

---

## Comment 4

> Username: pdimov  
> Created at: 2018-10-24 12:37:42 UTC  
> Url: https://github.com/boostorg/core/issues/43#issuecomment-432637646  

https://github.com/boostorg/core/commit/e9f986d11e046572aaf138f330c29f26182f794c

---

## Comment 5

> Username: akrzemi1  
> Created at: 2018-10-24 13:55:54 UTC  
> Url: https://github.com/boostorg/core/issues/43#issuecomment-432667100  

Thanks for the express fix.
