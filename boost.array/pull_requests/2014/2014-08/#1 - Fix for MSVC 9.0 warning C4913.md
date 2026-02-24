# #1 Fix for MSVC 9.0 warning C4913 [Closed]

> Username: sergiud  
> Created at: 2014-08-04 15:42:05 UTC  
> Updated at: 2015-05-14 02:59:27 UTC  
> Closed at: 2015-05-14 02:59:27 UTC  
> Url: https://github.com/boostorg/array/pull/1  

MSVC emitted the warning  
  
```  
user defined binary operator ',' exists but no overload could convert all operands, default built-in binary operator ',' used  
```  
  
for the `operator[]`. The fix splits the `BOOST_ASSERT_MSG` usage and the element access to two statements.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-02-09 14:27:53 UTC  
> Url: https://github.com/boostorg/array/pull/1#issuecomment-73516605  

The reason that this routine was written this way was to make the const version constexpr in C++11 (to match `std::array`).

---

## Comment 2

> Username: sergiud  
> Created_at: 2015-02-21 10:54:14 UTC  
> Url: https://github.com/boostorg/array/pull/1#issuecomment-75366600  

I see. Maybe then just suppress the warning using #pragmas?

---

## Comment 3

> Username: mclow  
> Created_at: 2015-05-14 02:59:27 UTC  
> Url: https://github.com/boostorg/array/pull/1#issuecomment-101889849  

I don't have access to VC9; if you want to try it and give me a new pull request; I will commit it.

---
