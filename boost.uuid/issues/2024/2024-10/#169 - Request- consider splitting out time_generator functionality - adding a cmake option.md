# #169 - Request: consider splitting out time_generator functionality / adding a cmake option? [Closed]

> Username: 13steinj  
> Created at: 2024-10-10 00:19:44 UTC  
> Updated at: 2024-10-18 00:13:29 UTC  
> Closed at: 2024-10-18 00:13:28 UTC  
> Url: https://github.com/boostorg/uuid/issues/169  

https://github.com/boostorg/uuid/blob/e7fcbc19825b2c81e90cedc3760bbb7ac39b5fb0/CMakeLists.txt#L25-L29  
  
It's unclear if the above lines are correct (considering I needed to manually change the tests on Clang to link against libatomic; but this may be an artifact of my organization's eccentric toolchain); however, by forcing all downstream consumers to link against `libatomic` on GCC; even those that don't use the time_generator functionality, this makes the library unsuitable for some high-performance environments (in our case; we intentionally don't link against `libatomic` since we want to use the intrinsics at all times or fail, and we do not supply `libatomic` on production machines for the same reason).  
  
I was originally going to suggest relying on autolinking, but then found out that apparently GCC doesn't support it.  
  
For what it's worth, I've gone the cmake-option route in our (fork? fork feels like the wrong term but I guess it's accurate?).  
  
It's also _possible_ that simply using Boost.Atomic instead of STL atomics would suffice as well (I'm unfamiliar with what Boost.Atomic does in such an environment / if it in some way also relies on `libatomic`).

---

## Comment 1

> Username: pdimov  
> Created at: 2024-10-10 08:21:04 UTC  
> Url: https://github.com/boostorg/uuid/issues/169#issuecomment-2404423092  

I agree that a CMake option seems the best way to approach this.  
  
Clang under Linux using libstdc++ very likely needs -latomic as well, I'm not sure about Clang/Linux using libc++. I don't see a simple way to detect the stdlib. One can always do a configure check, I suppose.  
  
My CMake CI only uses the system compiler, so testing for GCC was enough to make it pass.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-10-10 16:06:07 UTC  
> Url: https://github.com/boostorg/uuid/issues/169#issuecomment-2405519046  

Looks like -latomic is required on Linux even when using libc++: https://godbolt.org/z/hKv1z89aE

---

## Comment 3

> Username: 13steinj  
> Created at: 2024-10-10 16:44:20 UTC  
> Url: https://github.com/boostorg/uuid/issues/169#issuecomment-2405593825  

> I'm not sure about Clang/Linux using libc++.  
  
For reference on our eccentric toolchain; we use GCC + libstdc++ or Clang + libc++ (no "cross"), for historical (and, despite [issues](https://github.com/llvm/llvm-project/issues/30023#issuecomment-1856666131), quirky) reasons. So yeah, needed under clang + libc++ (bad timing between my response and you looking into it).

---

## Comment 4

> Username: pdimov  
> Created at: 2024-10-12 23:46:59 UTC  
> Url: https://github.com/boostorg/uuid/issues/169#issuecomment-2408747268  

CMake option added to develop.

---

## Comment 5

> Username: pdimov  
> Created at: 2024-10-18 00:13:28 UTC  
> Url: https://github.com/boostorg/uuid/issues/169#issuecomment-2420902827  

Merged to master.
