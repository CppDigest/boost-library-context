# #102 Provide error message and stacktrace with bad parameter to bjam.call() [Closed]

> Username: frenchtoast747  
> Created at: 2015-10-29 14:20:47 UTC  
> Updated at: 2021-10-02 22:20:13 UTC  
> Closed at: 2015-12-14 07:17:34 UTC  
> Url: https://github.com/boostorg/build/pull/102  

The previous implementation would just die when a bad parameter was passed in to `bjam.call()`.  
  
This tries to provide an error message describing the value that was invalid and falls back to just a TypeError in the event that `repr()` cannot be called on the PyObject. Both provide a stacktrace to where the call failed.

---

## Comment 1

> Username: frenchtoast747  
> Created_at: 2015-10-29 14:28:48 UTC  
> Url: https://github.com/boostorg/build/pull/102#issuecomment-152197123  

Should the Engine's version be updated when changes are made?

---

## Comment 2

> Username: swatanabe  
> Created_at: 2015-10-29 14:58:50 UTC  
> Url: https://github.com/boostorg/build/pull/102#issuecomment-152206184  

AMDG  
  
On 10/29/2015 08:28 AM, Aaron Boman wrote:  
  
> Should the Engine's version be updated when changes are made?  
  
  I only change the engine version for  
changes that can cause incompatibility.  
e.g. adding a builtin, significant  
bug fixes, etc.  This change seems  
purely cosmetic.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: frenchtoast747  
> Created_at: 2015-10-29 18:16:06 UTC  
> Url: https://github.com/boostorg/build/pull/102#issuecomment-152271700  

Alright, thanks!

---

## Comment 4

> Username: vprus  
> Created_at: 2015-12-14 07:17:34 UTC  
> Url: https://github.com/boostorg/build/pull/102#issuecomment-164365669  

Aaron,  
  
thanks for the patch, cherry-picked.

---
