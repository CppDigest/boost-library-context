# #431 - -Wmaybe-uninitialized clang in pragma in cpp_int.hpp [Closed]

> Username: ckormanyos  
> Created at: 2022-02-05 12:34:51 UTC  
> Updated at: 2022-02-06 11:13:48 UTC  
> Closed at: 2022-02-06 09:15:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/431  

In our recent correction to eliminate bogus warning on uninitialized, we inserted in `#pragma`s in the attempted suppression of `-maybe-uninitialized`.  
  
This results in an new unknown warning on `clang++` which reports that it is not aware or the meaning of this warning suppression.  
  
So in lines like [here](https://github.com/boostorg/multiprecision/blob/28b314828ddd06b3e2e3586758b398fc29ceeb89/include/boost/multiprecision/cpp_int.hpp#L39), I think we need to test for:  
  
```cpp  
#if defined(__GNUC__) && !defined(__clang__)  
// Suppressioin stuff  
#endif  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2022-02-06 11:13:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/431#issuecomment-1030810913  

Thank you John (@jzmaddock).  
  
I am going to also run a quick sanity check on macos-latest on a separate GHA also just to make absolutely sure we are also OK on those platforms. I'll report on the findings ASAP.
