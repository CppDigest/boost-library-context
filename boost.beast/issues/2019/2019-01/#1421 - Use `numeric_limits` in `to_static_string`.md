# #1421 - Use `numeric_limits` in `to_static_string` [Closed]

> Username: djarek  
> Created at: 2019-01-29 20:39:14 UTC  
> Updated at: 2019-02-16 13:49:05 UTC  
> Closed at: 2019-02-16 13:49:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1421  

The max size of the static string should be computed based on `numeric_limits`  rather than `sizeof`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-02 18:15:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1421#issuecomment-459986702  

I don't understand. The max size is measured in characters, and the size of the internal storage is N characters (not including the null) so how can this possibly be wrong:  
```  
    size_type constexpr  
    max_size() const  
    {  
        return N;  
    }  
```  
  
unless you are thinking of some absurd case where `N == pow(2, 64)-1` ? I could make `N` an unsigned short if you think that would help...

---

## Comment 2

> Username: djarek  
> Created at: 2019-02-10 21:50:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1421#issuecomment-462177689  

What I meant was that `sizeof()` doesn't necessarily determine the max value that can be stored in an integral type. This is quite esoteric and I doubt these kind of architectures would use boost anyway.  
  
Doing the "proper" max size requirement computation in C++11 in constexpr time seems not worth the cost of compilation time (it would need recursion, most likely).

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-02-10 21:50:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1421#issuecomment-462177750  

To be honest I still do not understand this problem

---

## Comment 4

> Username: djarek  
> Created at: 2019-02-10 22:36:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1421#issuecomment-462184858  

https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/impl/static_string.hpp#L621  
  
The number of digits needed to store an integer as a `static_string<N>` are computed based on `sizeof(Integer)`. On some platforms, `sizeof(char) == sizeof(int) && std::numeric_limits<int>::max() == 65535` is true. On such a platform there would be `int` values that would not fit into the result of `to_static_string()`.  
  
A proper solution would be to compute the size based on `log10(std::numeric_limits<T>::max()) + 2`, which requires recursion (or an approximation of  `log10`). TBH, this is probably way too much of an esoteric problem to handle.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-02-10 23:03:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1421#issuecomment-462189140  

OHHHH... `to_static_string`! I read the title as "Use `numeric_limits` in `static_string`"

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-02-10 23:07:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1421#issuecomment-462189681  

Now that I look at it, `to_static_string` return type should be `__implementation_defined__` or `__see_below__`

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-02-16 13:49:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1421#issuecomment-464348614  

not worth fixing
