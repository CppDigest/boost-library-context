# #100 Remove deprecated register [Closed]

> Username: SiliconA-Z  
> Created at: 2022-10-19 16:13:40 UTC  
> Updated at: 2022-10-19 20:29:50 UTC  
> Closed at: 2022-10-19 18:44:05 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/100  

No longer available in C++ 17, it is time we remove this keyword, including from the function signature itself.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-10-19 16:19:01 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/100#issuecomment-1284266717  

The answer to your question about #97 is that I don't have access to this platform and can't test the change, so I had asked the last person who contributed to it, @jwillikers, to check it (no idea whether CW/PPC requires the keyword for the inline assembly or not.)  
  
No response there, so if you vouch for the fact that things still work on CodeWarrior/PPC after removing `register`, I'll go ahead and apply this.

---

## Comment 2

> Username: SiliconA-Z  
> Created_at: 2022-10-19 16:23:24 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/100#issuecomment-1284272002  

I can attest that it causes no issue.

---

## Comment 3

> Username: jwillikers  
> Created_at: 2022-10-19 16:37:45 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/100#issuecomment-1284288501  

@pdimov Sorry, didn't see this until just now. We can test this, too.

---

## Comment 4

> Username: jwillikers  
> Created_at: 2022-10-19 18:40:36 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/100#issuecomment-1284425729  

I've pulled in your patch and our build failed with the following error message:  
  
```  
C:\.conan\f6ecba\1\include\boost\smart_ptr\detail\sp_counted_base_cw_ppc.hpp:74: a was not assigned to a GPR register (try using register qualifier)  
```

---

## Comment 5

> Username: pdimov  
> Created_at: 2022-10-19 18:44:05 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/100#issuecomment-1284429390  

Thanks, I suspected something like that. Closing this and #97, then.

---
