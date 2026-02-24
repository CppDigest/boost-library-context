# #183 Fixed for_each parameter type to accept mutable functor [Merged]

> Username: Flast  
> Created at: 2018-07-03 01:15:26 UTC  
> Updated at: 2019-03-07 12:31:16 UTC  
> Merged at: 2018-07-03 03:40:55 UTC  
> Closed at: 2018-07-03 03:40:55 UTC  
> Url: https://github.com/boostorg/fusion/pull/183  

fixes [#4427](https://svn.boost.org/trac10/ticket/4427)  
  
Now `for_each` takes functor by value, not reference.

---

## Comment 1

> Username: daminetreg  
> Created_at: 2019-02-13 14:20:08 UTC  
> Updated_at: 2019-02-13 14:30:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/183#issuecomment-463215875  

Hi @Flast,  
  
I'm a bit late on this but a colleague of mine ran into a regression after we upgraded the Boost version for his codebase.  
  
He was actually using a mutable member to be able to have a state in the for_each functor that he can use after a serialization action (I think it is a misuse of mutable if the const function behave differently on two calls though), like here https://rodgert.github.io/2014/09/09/type-driven-wire-protocols-with-boost-fusion-pt1/ :   
  
```cpp  
#include <boost/fusion/include/for_each.hpp>  
#include <boost/asio/buffer.hpp>  
  
struct reader {  
    mutable asio::const_buffer buf_;  
  
    explicit reader(asio::const_buffer buf)  
        : buf_(std::move(buf))  
    { }  
  
    template<class T>  
    void operator()(T & val) const {  
        // ...  
    }  
};  
  
template<typename T>  
std::pair<T, asio::const_buffer> read(asio::const_buffer b) {  
    reader r(std::move(b));  
    T res;  
    fusion::for_each(res, r);  
    return std::make_pair(res, r.buf_);  
}  
```  
  
Based on the Boost.Fusion documentation the code in this article is wrong, but happened to work until 1.68. So the question is shall we add an overload for reference based to avoid breaking too much code that will upgrade Boost at some point or are there any reason why we should keep the function copyable and copied in all cases ?  
  
The solution IMHO is to use a reference member or wrap the it in std::ref(f), and is what should always have been done according to documentation, but I think it's not obvious.  
  
Actually I was just remembered std::for_each also copies the functor, I think perhaps we could have the documentation give an insight on std::ref() being a good solution for having a state.

---

## Comment 2

> Username: Flast  
> Created_at: 2019-03-07 09:34:12 UTC  
> Url: https://github.com/boostorg/fusion/pull/183#issuecomment-470452909  

> The solution IMHO is to use a reference member or wrap the it in std::ref(f), and is what should always have been done according to documentation, but I think it's not obvious.  
>   
> Actually I was just remembered std::for_each also copies the functor, I think perhaps we could have the documentation give an insight on std::ref() being a good solution for having a state.  
  
I agree with you. We should write documentation about that, but where is the best place describing that? Copying is not a `for_each` specific behavior, but user will not find designated page...

---

## Comment 3

> Username: daminetreg  
> Created_at: 2019-03-07 12:31:16 UTC  
> Url: https://github.com/boostorg/fusion/pull/183#issuecomment-470507777  

Yes you are right it's hard to find a place. for_each is too specific, but we could add it on the general page about algorithms, and an example with for_each + mutability on the page https://www.boost.org/doc/libs/1_69_0/libs/fusion/doc/html/fusion/algorithm.html  
  
Therefore for_each search engine search would yield back for_each page and such an example. I can do that if you also think it's a good idea.

---
