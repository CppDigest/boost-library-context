# #97 Replace GetSystemTimeAsFileTime with GetSystemTimePreciseAsFileTime [Open]

> Username: pgawro  
> Created at: 2025-11-02 11:48:23 UTC  
> Updated at: 2025-11-23 16:54:34 UTC  
> Url: https://github.com/boostorg/winapi/pull/97  

According to Microsoft documentation this variant of GetSystemTime guarantiees to get highest posible level of precision (<1us). https://learn.microsoft.com/en-us/windows/win32/api/sysinfoapi/nf-sysinfoapi-getsystemtimepreciseasfiletime

---

## Comment 1

> Username: Lastique  
> Created_at: 2025-11-02 22:01:12 UTC  
> Url: https://github.com/boostorg/winapi/pull/97#issuecomment-3478404780  

1. As far as I can tell, you're not replacing anything but adding a new function. Please, change the commit message and PR accordingly.  
2. Since you're adding a new method, is there a Boost library that needs it? Boost.WinAPI is intended to be used only by other Boost libraries.  
3. `BOOST_HAS_GETSYSTEMTIMEPRECISEASFILETIME` is not defined anywhere. You need to implement proper target Windows version checks, including API family.

---

## Comment 2

> Username: pgawro  
> Created_at: 2025-11-23 14:06:47 UTC  
> Url: https://github.com/boostorg/winapi/pull/97#issuecomment-3567991428  

So to define BOOST_HAS_GETSYSTEMTIMEPRECISEASFILETIME  I should do that in main boost repo in config?

---

## Comment 3

> Username: Lastique  
> Created_at: 2025-11-23 16:54:34 UTC  
> Url: https://github.com/boostorg/winapi/pull/97#issuecomment-3568149244  

> So to define BOOST_HAS_GETSYSTEMTIMEPRECISEASFILETIME I should do that in main boost repo in config?  
  
You should only propose the macro to Boost.Config if it is supposed to be useful to other Boost libraries and possibly external users. I'm not sure that is the case. At least, I don't see `GetSystemTimePreciseAsFileTime` being used anywhere in Boost. Which comes back to my question 2 above.  
  
For the purposes of Boost.WinAPI, you only need to check for `BOOST_USE_WINAPI_VERSION` and possibly `BOOST_WINAPI_PARTITION_*` macros to enable or disable `GetSystemTimePreciseAsFileTime`.

---
