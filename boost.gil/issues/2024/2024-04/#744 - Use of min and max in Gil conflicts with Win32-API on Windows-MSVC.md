# #744 - Use of min and max in Gil conflicts with Win32-API on Windows/MSVC [Closed]

> Username: ckormanyos  
> Created at: 2024-04-17 02:55:38 UTC  
> Updated at: 2024-04-18 08:21:10 UTC  
> Closed at: 2024-04-18 08:21:10 UTC  
> Url: https://github.com/boostorg/gil/issues/744  

### Actual behavior  
  
I was recently using Boost.Gil with traditional Win32-API programming on MSVC (in Windows) and discovered a few conflicts with `windows.h`. Unfortunately `windows.h` defines `min()` and `max()` macros that conflict with the standard C++ ones.  
  
Defining `NOMINMAX` (or un-defining `BOOST_USE_WINDOWS_H)` _above_ inclusion of Windows-headers in client code works around this.  
  
I could PR the fix with trivial changes on 9 lines. Should I do this PR?  
  
Or would you just prefer to leave it, sort of _forcing_ clients to definine `NOMINMAX`?  
  
### Expected  behavior  
  
It might be preferable, however, to not require defining `NOMINMAX`.  
  
### C++ Minimal Working Example  
  
I found 9 instances in total, one example is [here](https://github.com/boostorg/gil/blob/322c4e2e191458383db0f2873dd3301f05a7d137/include/boost/gil/histogram.hpp#L139).  
  
Instead of:  
  
```cpp  
static constexpr Tuple min()  
```  
  
use  
  
```cpp  
static constexpr Tuple (min)()  
```  
  
### Environment  
  
MSVC 141, 142, 143 solely in combination with Boost.Gil headers and the inclusion of `<windows.h>`.  
  
- Version(s) 1.84, 1.85 and on master and develop branches.

---

## Comment 1

> Username: mloskot  
> Created at: 2024-04-17 07:29:46 UTC  
> Url: https://github.com/boostorg/gil/issues/744#issuecomment-2060570497  

Thanks @ckormanyos for reporting this.  
Although `(min)()` is a fine solution, I'm always unclear what is the Boost-wide convention to handle this annoyance. Do you perhaps know? I'd prefer to follow solution that is widely adopted by Boost developers and known to Boost users.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2024-04-17 07:50:29 UTC  
> Url: https://github.com/boostorg/gil/issues/744#issuecomment-2060616366  

>  what is the Boost-wide convention to handle this annoyance. Do you perhaps know?  
  
We use (min)() etc. in Math and Multiprecision. When I just started boosting, I learned or thought I learned from @jzmaddock that this parenthesis-based workaround is the _Boost_ _way_.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2024-04-17 08:17:07 UTC  
> Url: https://github.com/boostorg/gil/issues/744#issuecomment-2060674776  

I’ll just PR the trivial changes tonight and you can decide at your convenience if it’s a go or no-go.

---

## Comment 4

> Username: mloskot  
> Created at: 2024-04-17 08:44:33 UTC  
> Url: https://github.com/boostorg/gil/issues/744#issuecomment-2060726402  

> We use (min)() etc. in Math and Multiprecision...  
  
I'm used to this trick as well.  
  
> I’ll just PR the trivial changes tonight  
  
Thank you. I'm sure nobody will object it and your PR will be merged. Especially, that we had accepted similar fixes e.g. #328  
  
BTW, AFAICT, we mix both workarounds, the `()` and the `NOMINMAX` e.i. https://github.com/boostorg/gil/commit/87b7fdbcab359f7159a639aa4e210db765d73679 The former is more portable, of course, as build system agnostic.
