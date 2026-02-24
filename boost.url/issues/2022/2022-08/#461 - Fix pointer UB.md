# #461 - Fix pointer UB [Closed]

> Username: alandefreitas  
> Created at: 2022-08-26 17:22:22 UTC  
> Updated at: 2022-09-27 21:26:35 UTC  
> Closed at: 2022-09-27 20:44:31 UTC  
> Url: https://github.com/boostorg/url/issues/461  

https://github.com/CPPAlliance/url/blob/develop/include/boost/url/detail/impl/any_query_iter.ipp#L299  
  
is undefined behavior because that compares pointers that aren't guaranteed to belong to the same buffer  
  
We need to use `std::less` here.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-26 17:23:40 UTC  
> Url: https://github.com/boostorg/url/issues/461#issuecomment-1228743505  

we need a function like this  
https://github.com/CPPAlliance/url/blob/develop/include/boost/url/grammar/detail/impl/copied_strings.ipp#L23  
  
```cpp  
bool is_overlapping(string_view s, char const* begin, char const* end) noexcept;  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-27 21:25:58 UTC  
> Url: https://github.com/boostorg/url/issues/461#issuecomment-1260071492  

I thought I had it

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-27 21:26:35 UTC  
> Url: https://github.com/boostorg/url/issues/461#issuecomment-1260071981  

https://github.com/boostorg/url/blob/81cb06ec5e616d609ac01ef1b6a5d67eaa8448e2/include/boost/url/detail/move_chars.hpp#L28
