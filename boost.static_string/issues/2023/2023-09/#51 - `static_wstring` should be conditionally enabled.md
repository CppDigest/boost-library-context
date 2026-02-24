# #51 - `static_wstring` should be conditionally enabled [Closed]

> Username: jwt27  
> Created at: 2023-09-04 17:15:28 UTC  
> Updated at: 2023-10-24 18:17:15 UTC  
> Closed at: 2023-10-24 18:17:15 UTC  
> Url: https://github.com/boostorg/static_string/issues/51  

Hi!  
  
`static_wstring` fails to compile on platforms that don't have libc support  
for wide-character strings.  I got it to work by defining a no-op  
`std::swprintf(...)`, but obviously that isn't really a solution.  
  
However, boost::config has feature detection macros for this: `BOOST_NO_CWCHAR`  
and `BOOST_NO_SWPRINTF`.  So ideally, `static_wstring` should be conditionally  
enabled based on those.

---

## Comment 1

> Username: sdkrystian  
> Created at: 2023-09-07 11:13:04 UTC  
> Url: https://github.com/boostorg/static_string/issues/51#issuecomment-1709964402  

Reasonable, I'll take care of this soon

---

## Comment 2

> Username: alandefreitas  
> Created at: 2023-10-23 13:54:44 UTC  
> Url: https://github.com/boostorg/static_string/issues/51#issuecomment-1775260265  

Please note this was probably because of the standalone library. We would need two paths here.

---

## Comment 3

> Username: sdkrystian  
> Created at: 2023-10-24 14:42:39 UTC  
> Url: https://github.com/boostorg/static_string/issues/51#issuecomment-1777378417  

@alandefreitas I'm not sure if it's worth trying to replicate `BOOST_NO_CWCHAR` in standalone.. Might be better to just check for `cwchar` in standalone?

---

## Comment 4

> Username: alandefreitas  
> Created at: 2023-10-24 15:09:27 UTC  
> Url: https://github.com/boostorg/static_string/issues/51#issuecomment-1777445337  

Probably not. Whatever is easier in standalone. We can have a separate path for boost.
