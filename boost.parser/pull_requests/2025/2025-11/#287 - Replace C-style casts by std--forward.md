# #287 Replace C-style casts by std::forward [Closed]

> Username: andreasbuhr  
> Created at: 2025-11-01 07:07:18 UTC  
> Updated at: 2025-11-01 16:59:22 UTC  
> Closed at: 2025-11-01 16:09:57 UTC  
> Url: https://github.com/boostorg/parser/pull/287  

Note that std::forward<T> ist just a  
static_cast<T&&>, see  
https://eel.is/c++draft/forward#3  
  
I hope that this fixes some of the errors in Drone-CI with Clang 13 like the following  
```  
./boost/parser/search.hpp:55:36: error: call to implicitly-deleted copy constructor of 'boost::parser::detail::text::utf16_view<boost::parser::detail::text::detail::owning_view<boost::parser::subrange<const char *, boost::parser::detail::text::null_sentinel_t>>>'  
                            return (R &&) r;  
```

---

## Comment 1

> Username: andreasbuhr  
> Created_at: 2025-11-01 07:26:00 UTC  
> Url: https://github.com/boostorg/parser/pull/287#issuecomment-3475891761  

Unfortunately it does not fix the Drone-CI errors.   
Anyway, merge or discard, as you like.

---

## Comment 2

> Username: tzlaine  
> Created_at: 2025-11-01 16:09:57 UTC  
> Url: https://github.com/boostorg/parser/pull/287#issuecomment-3476489708  

I'm rejecting this because `(T &&)x` and `std::forward<T>(x)` are identical semantically, but the latter is longer to type, and instantiates more templates (and so is longer to compile too).

---
