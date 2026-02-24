# #338 - Outdated Sparc processor list breaks atomic operations on Niagara processors [Open]

> Username: DerDakon  
> Created at: 2018-09-07 19:52:47 UTC  
> Updated at: 2021-06-26 03:09:48 UTC  
> Url: https://github.com/boostorg/build/issues/338  

See downstream bug with logs: https://bugs.gentoo.org/646234  
  
The code tries to autodetect the processor architecture, and overrides (!) the CXXFLAGS by the user by appending the result of the autodetection. Sadly this does not know about anything newer than ultrasparc3 (gcc knows about -mcpu=niagara since 4.2). For all unknown processors it forces a fallback to v7, which is unnecessary since it's the compiler default if no architecture is passed, and it is extremely old and does not know about atomic operations on anything larger than single bytes. And to make it worse it doesn't link to libatomic then.  
  
My suggestion is to entirely drop that code and just let the user pass what is deemed appropiate. This especially makes it much easier if I want to build a package for a machine that does not match the current type.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:09 UTC  
> Url: https://github.com/boostorg/build/issues/338#issuecomment-859203396  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 2

> Username: DerDakon  
> Created at: 2021-06-11 05:23:55 UTC  
> Url: https://github.com/boostorg/build/issues/338#issuecomment-859275756  

The code is still there unchanged, so I guess it has to be moved.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:10 UTC  
> Url: https://github.com/boostorg/build/issues/338#issuecomment-868936369  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
