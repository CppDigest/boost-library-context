# #60 - Missing operator in less_than_comparable2? [Closed]

> Username: MFHava  
> Created at: 2019-05-21 09:16:50 UTC  
> Updated at: 2019-05-21 14:41:19 UTC  
> Closed at: 2019-05-21 14:40:54 UTC  
> Url: https://github.com/boostorg/utility/issues/60  

Due to a compilation error, I just had a look at Boost.Operators and think there's an inconcistency in `less_than_comparable2`.  
From my understand, it should generate:  
  
- `operator<=(T, U)`  
- `operator>=(T, U)`  
- `operator> (T, U)`  
- `operator< (U, T)`  
- `operator<=(U, T)`  
- `operator>=(U, T)`  
- `operator> (U, T)`  
  
based on the user-supplied `operator<(T, U)` - aka extend `less_than_comparable1` for two types (1), but `operator>(T, U)` is actually missing in `less_than_comparable2`.  
Is this a bug or a deliberate design?  
  
(1) Note that `equality_comparable2` exhibits exactly that behavior compared to `equality_comparable1`.

---

## Comment 1

> Username: d-frey  
> Created at: 2019-05-21 14:40:54 UTC  
> Url: https://github.com/boostorg/utility/issues/60#issuecomment-494420763  

`less_than_comparable2` requires `T<U` and `T>U` to be supplied by the user, as the latter can not simply be derived from the former. Reversing the arguments of `T<U` yields `U<T` and `T<U` does not necessarily allow that. Even if it would work, it would probably convert the arguments from `T` to `U` and from `U` to `T`.  
  
The operators library generates new operators based on existing one by only allowing to swap the existing arguments or negate the result of another comparison. This is not the case here.
