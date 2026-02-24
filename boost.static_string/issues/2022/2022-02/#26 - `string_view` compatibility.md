# #26 - `string_view` compatibility [Closed]

> Username: timblechmann  
> Created at: 2022-02-04 04:11:58 UTC  
> Updated at: 2023-01-19 22:57:34 UTC  
> Closed at: 2023-01-19 22:57:34 UTC  
> Url: https://github.com/boostorg/static_string/issues/26  

`static_string` is really useful when trying to avoid heap allocations. starting to use it, i came across a few issues when it comes to using it with APIs that use `std::string_view`  
  
* one cannot create (or assign) a `static_string` from a `string_view` (but one can create it from a `std::string`)  
* one cannot compare `static_string` with a `string_view` (one also cannot compare it to `std::string`, though)  
* one cannot use `static_string` in APIs that take `string_view`. this one is tricky, as it would require an implicit `operator string_view`, which is probably nothing we want to have. `std::string` gets around this limitation via a ctor signature of `string_view` that takes a `std::string` ... not ideal, but one could potentially add an `as_string_view` method to avoid adding too much noise  
  
thoughts?

---

## Comment 1

> Username: sdkrystian  
> Created at: 2022-02-04 05:47:21 UTC  
> Url: https://github.com/boostorg/static_string/issues/26#issuecomment-1029679012  

The lack of a converting constructor for`std::string_view` seems like a bit of an oversight. Same for the lack of equality comparison operators for `std:: string` and `std::string_view`. I'll have to give the last one some thought.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-02-04 06:17:44 UTC  
> Url: https://github.com/boostorg/static_string/issues/26#issuecomment-1029692164  

Try making `static_string` convertible to `boost::core::detail::string_view`

---

## Comment 3

> Username: sylveon  
> Created at: 2022-02-06 00:18:15 UTC  
> Updated at: 2022-02-06 00:19:22 UTC  
> Url: https://github.com/boostorg/static_string/issues/26#issuecomment-1030719137  

I'm trying to use static_string to be able to pass compile-time format strings around (because std::format_string was not made public in C++20), so a constexpr conversion operator to std::basic_string_view would be required in this use case.  
  
And I use the library in standalone mode so going through boost's string_view isn't really a solution.

---

## Comment 4

> Username: timblechmann  
> Created at: 2022-05-01 03:07:57 UTC  
> Url: https://github.com/boostorg/static_string/issues/26#issuecomment-1114114400  

#29 implements comparison operators

---

## Comment 5

> Username: alandefreitas  
> Created at: 2023-01-12 02:42:26 UTC  
> Updated at: 2023-01-12 02:42:42 UTC  
> Url: https://github.com/boostorg/static_string/issues/26#issuecomment-1379740584  

Hi @timblechmann   
  
I think updating the string type in `config.hpp` to `boost::core::detail::string_view` would solve most problems here.  
  
```cpp  
template<typename CharT, typename Traits>  
using basic_string_view =  
#ifndef BOOST_STATIC_STRING_STANDALONE  
  boost::core::detail::basic_string_view<CharT>;  
#else  
  std::basic_string_view<CharT, Traits>;  
#endif  
```  
  
A few questions just to make sure  
  
> one cannot create (or assign) a static_string from a string_view (but one can create it from a std::string)  
  
Can't the   
  
```cpp  
template<typename T, typename = detail::enable_if_viewable_t<T, CharT, Traits>>  
  explicit  
  BOOST_STATIC_STRING_CPP14_CONSTEXPR  
  basic_static_string(const T& t)  
  {  
    assign(t);  
  }  
```  
  
overload do it?  
  
I understand why `boost::basic_string_view` wouldn't work here, because it explicitly forbids construction from `std::string_view` (That's why `boost::core::detail::string_view` was created). But I don't see why `std::basic_string_view` wouldn't work (unless this issue is about making it implicit or you're mixing the boost version of the library with `std::string_view`).  
  
> one cannot compare static_string with a string_view (one also cannot compare it to std::string, though)  
  
Yes. This one is true. The `enable_if_viewable_t` overloads need to be created.  
  
> one cannot use static_string in APIs that take string_view.   
  
Can't the  
  
```cpp  
BOOST_STATIC_STRING_CPP11_CONSTEXPR  
  operator string_view_type() const noexcept  
  {  
    return string_view_type(data(), size());  
  }  
```  
  
overload do it?   
  
Is it because you're not using the boost version of the library and `std::string_view` for the API? So same problem as before. `core::detail::string_view` also fixes this. Or its pattern to make it convertible to both `core::detail::string_view` and `std::string_view`.  
  
> an implicit operator string_view, which is probably nothing we want to have.   
  
This sounds fine to me. An implicit constructor from `string_view` is what's usually controversial.  
  
So yes. I think we can fix everything with `boost::core::detail::string_view` and a new overload for the comparison operators.

---

## Comment 6

> Username: timblechmann  
> Created at: 2023-01-12 05:18:01 UTC  
> Url: https://github.com/boostorg/static_string/issues/26#issuecomment-1379827140  

> I think updating the string type in config.hpp to boost::core::detail::string_view would solve most problems here.  
  
afaict it will merely shift the problems: as one can either interact with `boost::string_view` or with `std::string_view`. for some projects this may be sufficient, but other may have a mix of both types in their APIs (unfortunately i'm interacting with a mix of std and boost string_views in my codebase).  
  
compare:  
https://godbolt.org/z/YGM9198xr  
  
(btw, it seems that the standalone mode misses an include?)

---

## Comment 7

> Username: alandefreitas  
> Created at: 2023-01-12 16:00:07 UTC  
> Url: https://github.com/boostorg/static_string/issues/26#issuecomment-1380612072  

> merely shift the problems: as one can either interact with boost::string_view or with std::string_view  
  
Not really, because `boost::core::detail::string_view` is convertible from/to `std::string_view`.
