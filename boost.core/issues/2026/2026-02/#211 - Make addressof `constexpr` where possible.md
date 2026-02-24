# #211 - Make addressof `constexpr` where possible [Closed]

> Username: akrzemi1  
> Created at: 2026-02-01 11:31:32 UTC  
> Updated at: 2026-02-01 14:06:28 UTC  
> Closed at: 2026-02-01 14:06:27 UTC  
> Url: https://github.com/boostorg/core/issues/211  

I need to take addresses of objects in generic `constexpr` function templates. I need to be prepared for some of the types to have the overloaded unary `&` operator.  
  
Can you make `boost::addressof`:  
 * `constexpr`, wherever possible,  
 * still not drag header `<memory>`?  
  
That might require making a specialization for `boost::addressof` for types without the overloaded `&`.

---

## Comment 1

> Username: pdimov  
> Created at: 2026-02-01 12:12:11 UTC  
> Url: https://github.com/boostorg/core/issues/211#issuecomment-3830961258  

It already is, see https://github.com/boostorg/core/blob/a90a31934fe8bcb6e6be6dfea77b80492c7b6c81/include/boost/core/addressof.hpp#L19-L32

---

## Comment 2

> Username: akrzemi1  
> Created at: 2026-02-01 12:29:42 UTC  
> Url: https://github.com/boostorg/core/issues/211#issuecomment-3830981054  

Thanks.  
So this covers the case where a  compiler offers `__builtin_addressof`.  
For the remaining cases, it should still be possible to offer a `constexpr` version for cases where we know the type is not messing with `operator&`, no?

---

## Comment 3

> Username: pdimov  
> Created at: 2026-02-01 12:42:06 UTC  
> Url: https://github.com/boostorg/core/issues/211#issuecomment-3830994624  

Maybe; how could we know that?

---

## Comment 4

> Username: akrzemi1  
> Created at: 2026-02-01 12:44:17 UTC  
> Updated at: 2026-02-01 12:44:54 UTC  
> Url: https://github.com/boostorg/core/issues/211#issuecomment-3830996953  

```c++  
template <typename T, typename = void>  
struct has_bad_address : ::std::false_type {};  
  
template <typename T>  
struct has_bad_address<T, decltype(operator&(std::declval<T>()))> : ::std::true_type {};  
  
template <typename T>  
struct has_bad_address<T, decltype(std::declval<T>().operator&())> : ::std::true_type {};  
```

---

## Comment 5

> Username: pdimov  
> Created at: 2026-02-01 13:23:03 UTC  
> Url: https://github.com/boostorg/core/issues/211#issuecomment-3831040167  

Seems to me that the current code already does this.  
  
Do you have a specific example that doesn't work?

---

## Comment 6

> Username: akrzemi1  
> Created at: 2026-02-01 13:29:20 UTC  
> Url: https://github.com/boostorg/core/issues/211#issuecomment-3831050295  

Currently everything works for me. But I only have access to newer versions of GCC and Clang. I am only considering switching the implementation of `boost::optional` to `constexpr` in C++14. I am concerned that I may break something.  
  
I  drew my conclusion from the fact that word "constexpr" appears only once in `<addressof.hpp>`: in the specialization for `__builtin_addressof`. I would have expected "constexpr" in at least one more place.

---

## Comment 7

> Username: pdimov  
> Created at: 2026-02-01 13:30:13 UTC  
> Url: https://github.com/boostorg/core/issues/211#issuecomment-3831052105  

That's because it supports C++03, so `constexpr` would have been a compile error.

---

## Comment 8

> Username: akrzemi1  
> Created at: 2026-02-01 13:34:03 UTC  
> Url: https://github.com/boostorg/core/issues/211#issuecomment-3831060148  

Would it be possible to use `BOOST_CONSTEXPR`?

---

## Comment 9

> Username: pdimov  
> Created at: 2026-02-01 13:35:19 UTC  
> Url: https://github.com/boostorg/core/issues/211#issuecomment-3831062574  

That's what the code uses, yes.

---

## Comment 10

> Username: pdimov  
> Created at: 2026-02-01 13:37:34 UTC  
> Url: https://github.com/boostorg/core/issues/211#issuecomment-3831066391  

The overload that uses `__builtin_addressof` is this: https://github.com/boostorg/core/blob/a90a31934fe8bcb6e6be6dfea77b80492c7b6c81/include/boost/core/addressof.hpp#L37  
  
The constexpr overloads that do not are:  
  
https://github.com/boostorg/core/blob/a90a31934fe8bcb6e6be6dfea77b80492c7b6c81/include/boost/core/addressof.hpp#L244  
  
https://github.com/boostorg/core/blob/a90a31934fe8bcb6e6be6dfea77b80492c7b6c81/include/boost/core/addressof.hpp#L254

---

## Comment 11

> Username: akrzemi1  
> Created at: 2026-02-01 13:45:56 UTC  
> Updated at: 2026-02-01 14:04:16 UTC  
> Url: https://github.com/boostorg/core/issues/211#issuecomment-3831078816  

Confirmed.  
~Now, it looks to me at least one of the remaining ones could also use `BOOST_CONSTEXPR`. It is my understanding that this is how `BOOST_CONSTEXPR` works: on compilers without `constexpr` it expands to nothing.~

---

## Comment 12

> Username: akrzemi1  
> Created at: 2026-02-01 14:06:28 UTC  
> Url: https://github.com/boostorg/core/issues/211#issuecomment-3831102484  

My apologies.   
They are already `constexpr`. I must have some brain damage.   
`boost::addressof` is already "as constexpr as possible".
