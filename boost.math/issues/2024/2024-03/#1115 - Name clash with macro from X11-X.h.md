# #1115 - Name clash with macro from X11/X.h [Closed]

> Username: iv-m  
> Created at: 2024-03-27 09:11:02 UTC  
> Updated at: 2024-03-28 14:46:36 UTC  
> Closed at: 2024-03-28 06:53:18 UTC  
> Url: https://github.com/boostorg/math/issues/1115  

Xorg protocol definitions include `Complex` macro, which is defined to 0 (see e.g. https://gitlab.freedesktop.org/xorg/proto/xorgproto/-/blob/xorgproto-2024.1/include/X11/X.h#L552).  
  
It turns out that there is code out there that includes both `X11/X.h` (through wxWidgets) and `boost/math/tools/roots.hpp`, which gives the following error:  
  
```  
[...]  
/usr/include/boost/math/tools/roots.hpp: At global scope:  
/usr/include/boost/math/tools/roots.hpp:787:16: error: expected identifier before numeric constant  
  787 | template<class Complex, class F>  
      |                ^~~~~~~  
/usr/include/boost/math/tools/roots.hpp:787:16: error: expected '>' before numeric constant  
/usr/include/boost/math/tools/roots.hpp:788:1: error: expected unqualified-id before numeric constant  
  788 | Complex complex_newton(F g, Complex guess, int max_iterations = std::numeric_limits<typename Complex::value_type>::digits)  
      | ^~~~~~~  
[... and so on ...]  
```  
  
While it's not a bug in Boost.Math as it is, it makes sense to me to rename the template parameter to a less common word,  to avoid this and, potentially, other similar clashes.

---

## Comment 1

> Username: iv-m  
> Created at: 2024-03-28 07:21:21 UTC  
> Url: https://github.com/boostorg/math/issues/1115#issuecomment-2024563178  

Thank you.

---

## Comment 2

> Username: mborland  
> Created at: 2024-03-28 07:35:19 UTC  
> Url: https://github.com/boostorg/math/issues/1115#issuecomment-2024582121  

> Thank you.  
  
No problem. I asked permission to merge into Boost 1.85 since its mid-release cycle (release date 10 April). I expect it'll make it.

---

## Comment 3

> Username: mborland  
> Created at: 2024-03-28 14:46:35 UTC  
> Url: https://github.com/boostorg/math/issues/1115#issuecomment-2025407282  

Merged into master for 1.85
