# #21 Fix MSVC warnings in tests [Merged]

> Username: MarcelRaad  
> Created at: 2016-07-19 09:03:51 UTC  
> Updated at: 2023-01-01 17:58:22 UTC  
> Merged at: 2023-01-01 17:52:46 UTC  
> Closed at: 2023-01-01 17:52:46 UTC  
> Url: https://github.com/boostorg/algorithm/pull/21  

MSVC complains about narrowing conversions and unreferences parameters.  
This makes all tests almost level 4 warning clean on MSVC 14.

---

## Comment 1

> Username: Flast  
> Created_at: 2016-07-30 02:45:59 UTC  
> Url: https://github.com/boostorg/algorithm/pull/21#discussion_r72880627  

`std::begin/end` is came from C++11, so I think those should be replaced with `boost::begin/end`.

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2022-12-30 14:45:37 UTC  
> Url: https://github.com/boostorg/algorithm/pull/21#issuecomment-1367954231  

Rebased and removed the two commits which are already integrated. Only the warning fixes are left now.

---

## Comment 3

> Username: mclow  
> Created_at: 2022-12-30 15:25:46 UTC  
> Url: https://github.com/boostorg/algorithm/pull/21#issuecomment-1367971318  

IIRC, the `unsigned` in `hex` is there to prevent warnings on other compilers.

---

## Comment 4

> Username: mclow  
> Created_at: 2022-12-30 15:29:56 UTC  
> Url: https://github.com/boostorg/algorithm/pull/21#issuecomment-1367972931  

In the `testX` checks, I'd rather change `dest` to a `size_t`, since that's what `std::distance` returns.

---

## Comment 5

> Username: MarcelRaad  
> Created_at: 2022-12-30 22:10:40 UTC  
> Url: https://github.com/boostorg/algorithm/pull/21#issuecomment-1368106037  

> IIRC, the unsigned in hex is there to prevent warnings on other compilers.  
  
OK, I reverted that one.  
  
> In the testX checks, I'd rather change dest to a size_t, since that's what std::distance returns.  
  
Right, that's better! It's ptrdiff_t rather than size_t, but it also applies to the operator+ used in the other tests. I changed that now.

---
