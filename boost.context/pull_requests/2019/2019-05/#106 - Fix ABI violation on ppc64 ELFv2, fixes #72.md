# #106 Fix ABI violation on ppc64 ELFv2, fixes #72 [Merged]

> Username: shawnanastasio  
> Created at: 2019-05-04 01:24:28 UTC  
> Updated at: 2019-05-04 08:43:01 UTC  
> Merged at: 2019-05-04 08:43:01 UTC  
> Closed at: 2019-05-04 08:43:01 UTC  
> Url: https://github.com/boostorg/context/pull/106  

The existing ontop_fcontext implementation for ppc64 ELFv2  
violates the ABI by not storing the callback entry address  
in %r12 before branching. This results in crashes on this  
platform.  
  
This commit addresses this and allows the context library  
to function as expected on ppc64 platforms using the ELFv2 ABI.  
  
The OpenPOWER ELFv2 ABI states on page 41, section 2.2.1.1: "r12 [...] Function entry address at the global entry point."

---

## Comment 1

> Username: q66  
> Created_at: 2019-05-04 01:40:54 UTC  
> Url: https://github.com/boostorg/context/pull/106#issuecomment-489283632  

I can confirm that the testsuite passes now (testing on Linux, POWER8 little endian).

---

## Comment 2

> Username: wak-google  
> Created_at: 2019-05-04 01:42:34 UTC  
> Url: https://github.com/boostorg/context/pull/106#issuecomment-489283744  

This is exactly what I was seeing as the issue, thanks.

---

## Comment 3

> Username: olk  
> Created_at: 2019-05-04 08:42:57 UTC  
> Url: https://github.com/boostorg/context/pull/106#issuecomment-489307573  

ty

---
