# #1948 - Feature request: a subclass for http::empty_body for HEAD response [Closed]

> Username: leha-bot  
> Created at: 2020-05-08 13:26:40 UTC  
> Updated at: 2020-06-12 12:35:09 UTC  
> Closed at: 2020-06-12 12:35:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1948  

It would help with generic code as the `beast::http::empty_body` class intentionally doesn't store any data and doesn't have the `size()` method.  
  
I implemented the example class in my internal server [here](https://gist.github.com/leha-bot/4a88fb668feaa43340538189ec706e78)  
  
The generic code-"consumer" is factory function for creating any kind of response - [here](https://gist.github.com/leha-bot/5a1917bda578986a66923abdacbe52bf).  
  
Thank you for your cool C++ library!

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-05-08 13:27:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1948#issuecomment-625815532  

You'd like a size function that returns zero when the target doesn't have a size method?

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-05-08 13:35:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1948#issuecomment-625818816  

```  
#include <cstddef>  
#include <iostream>  
#include <type_traits>  
  
  
struct thing_with_size  
{  
    std::size_t size() const { return s; }  
  
    std::size_t s;  
};  
  
struct thing_with_no_size  
{  
  
};  
  
  
template<class T, class = void>  
struct get_size_op  
{  
    std::size_t operator()(T const&) const  
    {  
        return 0;  
    }  
};  
  
template<class T>  
struct get_size_op<T, std::void_t<decltype(std::declval<T const& >().size())> >  
{  
    std::size_t operator()(T const& arg) const  
    {  
        return arg.size();  
    }  
};  
  
template<class Type>  
std::size_t get_size(Type&& x)  
{  
    auto op = get_size_op<std::decay_t<Type>>();  
    return op(x);  
}  
  
int main()  
{  
    auto a = thing_with_size { 10 };  
    auto b = thing_with_no_size {  };  
  
    std::cout << get_size(a) << std::endl;  
    std::cout << get_size(b) << std::endl;  
}  
```  
  
godbolt demo: https://godbolt.org/z/aQP437

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-06-07 15:00:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1948#issuecomment-640231965  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-06-12 12:35:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1948#issuecomment-643247174  

No response. Assuming my workaround was sufficient.
