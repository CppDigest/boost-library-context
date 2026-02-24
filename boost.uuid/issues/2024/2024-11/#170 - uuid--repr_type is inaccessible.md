# #170 - uuid::repr_type is inaccessible [Closed]

> Username: tobias-loew  
> Created at: 2024-11-11 10:23:27 UTC  
> Updated at: 2025-09-04 00:47:17 UTC  
> Closed at: 2025-09-04 00:47:17 UTC  
> Url: https://github.com/boostorg/uuid/issues/170  

The new internal `data_type` has the two `public` operators  
```  
        BOOST_CXX14_CONSTEXPR operator repr_type& () noexcept { return repr_; }  
        constexpr operator repr_type const& () const noexcept { return repr_; }  
```  
but their return type `repr_type` is `private` and therefore inaccessible from outside.  
As the operators are not used internally, I assume, they're to be used be users, so `repr_type` should also be `public`.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-11-11 10:59:18 UTC  
> Url: https://github.com/boostorg/uuid/issues/170#issuecomment-2467888065  

`repr_type` is just an alias for `std::uint8_t[16]`. It's not intended to be named.

---

## Comment 2

> Username: tobias-loew  
> Created at: 2024-11-11 13:58:46 UTC  
> Url: https://github.com/boostorg/uuid/issues/170#issuecomment-2468245573  

Ok. But calling the user-defined conversion would then always require a typedef, since conversion-type-id does not allow array-brackets (https://en.cppreference.com/w/cpp/language/cast_operator).  
IMHO, it would be nice also having functions (e.g. `as_array()`) returning a [const] reference to `repr_`.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-11-11 14:05:52 UTC  
> Url: https://github.com/boostorg/uuid/issues/170#issuecomment-2468261113  

The conversion to `repr_type&` is an implementation detail, only intended to support old code that used `data` as a data member. So if you're using it to obtain access to the underlying array, it might indeed be better to provide a documented way to do that.  
  
Out of curiosity, for what would you use this function?

---

## Comment 4

> Username: tobias-loew  
> Created at: 2024-11-11 14:20:01 UTC  
> Url: https://github.com/boostorg/uuid/issues/170#issuecomment-2468292347  

I've got a template-based serialization-library (not Boost.Serialization), which works out-of-the box, when it can deduce the full array-information (type and size).  It used to work with code like `ar & uuid_variable.data`  using Boost 1.85
