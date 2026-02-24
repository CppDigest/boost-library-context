# #42 - Loss of data (C4244) warnings in MSVC 64-bit build [Closed]

> Username: SpareSimian  
> Created at: 2019-07-22 10:46:35 UTC  
> Updated at: 2022-09-17 20:57:26 UTC  
> Closed at: 2022-09-17 20:57:25 UTC  
> Url: https://github.com/boostorg/locale/issues/42  

Building with MSVC compilers for 64-bit results in several C4244 warnings in api.hpp, due to passing the difference of two pointers (ptrdiff_t) to Win APIs expecting an int. The attached patch adds casts to silence the warnings.  
  
[api.hpp.diff.txt](https://github.com/boostorg/locale/files/3416870/api.hpp.diff.txt)

---

## Comment 1

> Username: OlafvdSpek  
> Created at: 2019-07-26 09:18:23 UTC  
> Url: https://github.com/boostorg/locale/issues/42#issuecomment-515375926  

C4244 is Loss of data, Loss of precision is a floating point thing.

---

## Comment 2

> Username: OlafvdSpek  
> Created at: 2019-07-26 09:19:09 UTC  
> Updated at: 2019-07-26 09:19:23 UTC  
> Url: https://github.com/boostorg/locale/issues/42#issuecomment-515376132  

Wouldn't a runtime check on size < 2GB be required as well?

---

## Comment 3

> Username: SpareSimian  
> Created at: 2019-07-26 09:44:52 UTC  
> Url: https://github.com/boostorg/locale/issues/42#issuecomment-515384487  

Issue title corrected, thanks.   
  
We could certainly put an assert in there to check the size argument. This is really a case of Windows using the wrong type, but it's probably necessary for compatibility. I suggest a test against std::numeric_limits<int>::max(), which would probably get optimized away if int is the same size as ptrdiff_t.

---

## Comment 4

> Username: Flamefire  
> Created at: 2022-09-17 20:57:25 UTC  
> Url: https://github.com/boostorg/locale/issues/42#issuecomment-1250139407  

Fixed by #86
