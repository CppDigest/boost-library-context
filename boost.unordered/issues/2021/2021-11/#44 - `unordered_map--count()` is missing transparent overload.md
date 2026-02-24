# #44 - `unordered_map::count()` is missing transparent overload [Closed]

> Username: cmazakas  
> Created at: 2021-11-19 20:50:21 UTC  
> Updated at: 2021-11-23 23:50:29 UTC  
> Closed at: 2021-11-23 23:50:29 UTC  
> Url: https://github.com/boostorg/unordered/issues/44  

[P1690R1](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p1690r1.html) added a transparent overload of `count()` that avoids creating an intermediate object when getting the count.  
  
See: https://en.cppreference.com/w/cpp/container/unordered_map/count

---

## Comment 1

> Username: cmazakas  
> Created at: 2021-11-22 21:34:18 UTC  
> Updated at: 2021-11-22 21:43:30 UTC  
> Url: https://github.com/boostorg/unordered/issues/44#issuecomment-975939129  

@glenfe @pdimov   
  
One thing I didn't realize until I started development, no one seems to specify the argument order for the `KeyEqual` callable.  
  
The way the library is now, it goes: `eq(user_supplied_type, stored_type)` taken from [here](https://github.com/boostorg/unordered/blob/c8abaf32ee803711d84f24028ee7e855e1346913/include/boost/unordered/detail/implementation.hpp#L3590);  
  
Should we try and support flipping the user-provided `KeyEqual` if the user's `operator==` implementation wasn't written this way?

---

## Comment 2

> Username: pdimov  
> Created at: 2021-11-22 21:45:04 UTC  
> Url: https://github.com/boostorg/unordered/issues/44#issuecomment-975946085  

No.
