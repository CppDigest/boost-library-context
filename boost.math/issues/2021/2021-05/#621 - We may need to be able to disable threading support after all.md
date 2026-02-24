# #621 - We may need to be able to disable threading support after all. [Closed]

> Username: jzmaddock  
> Created at: 2021-05-02 17:08:53 UTC  
> Updated at: 2024-02-22 10:34:39 UTC  
> Closed at: 2024-02-22 10:34:39 UTC  
> Url: https://github.com/boostorg/math/issues/621  

Leaving aside @ckormanyos building on the metal, msvc with the /clr options does not support any of the std threading headers: `<mutex>`, `<thread>`, `<atomic>` etc.  
  
Currently building in this mode triggers a #error in cxx03_warn.hpp which prevents **any** part of the library being used.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-05-02 17:26:44 UTC  
> Updated at: 2021-05-02 17:27:43 UTC  
> Url: https://github.com/boostorg/math/issues/621#issuecomment-830842675  

> on the metal, msvc with the /clr options does not support any of the std threading headers  
  
Oh yes, I had completely forgotten that. It was really, really a long time ago. At that time, I wanted to export some functions to Microsoft's so-called _managed C++_, using `cli_ext`headers. This is indeed a limitation.  
  
On my huge list of things to do was to make a cursory glance at what actually needs threads, mutexes and atomics. For the special functions, it seems rather limited to `tgamma`and friends (in the direct use of Bernoulli number tables actually). I have not seriously looked into other parts like quadrature, etc.  
  
I would be interested if we could pursue a way to handily and reliably disable threading, for metal, python exports, CLR/.NET exports and the lot.

---

## Comment 2

> Username: mborland  
> Created at: 2024-02-22 10:34:39 UTC  
> Url: https://github.com/boostorg/math/issues/621#issuecomment-1959157519  

This seems to be pretty well supported at this time. Closing to cleanup our open issues.
