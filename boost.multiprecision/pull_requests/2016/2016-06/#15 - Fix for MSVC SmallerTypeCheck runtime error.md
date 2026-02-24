# #15 Fix for MSVC SmallerTypeCheck runtime error [Closed]

> Username: fun4jimmy  
> Created at: 2016-06-07 12:22:38 UTC  
> Updated at: 2016-06-16 12:07:59 UTC  
> Closed at: 2016-06-16 12:07:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/15  

The assignment of carry to p in eval_multiply generates a runtime error in non-optimized MSVC builds which have SmallerTypeCheck enabled. As carry is twice the size of p and the top bits are non-zero the check errors saying that some bits are being ignored. This is Microsoft's recommended fix of masking the unwanted bits when assigning to the smaller type.

---

## Comment 1

> Username: fun4jimmy  
> Created_at: 2016-06-07 13:11:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/15#issuecomment-224275605  

There is a`#pragma runtime_checks` which I've tried to use as a temporary workaround but I can't seem to get it working with a generic template function. I've found [this stackoverflow question](http://stackoverflow.com/questions/28473206/how-to-disable-runtime-checks-for-function-templates-in-visual-studio-c) that seems to be asking about the same problem but there are no answers as to how to work around it.

---

## Comment 2

> Username: fun4jimmy  
> Created_at: 2016-06-07 13:25:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/15#issuecomment-224279243  

The code generating the runtime error is:  
  
``` cpp  
const int64_t Input = 135000000;  
const uint32_t Multiplier = 135000000;  
boost::multiprecision::int128_t Value = Input;  
Value *= Multiplier;  
```

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2016-06-15 18:10:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/15#issuecomment-226271989  

This is the tip of a very large iceberg - running the regression tests with /RTCc results in hundreds of these issues - if I can fix them I will, but I'd prefer not to add unnecessary bitmask operations just to keep MSVC happy.  I realize msvc will optimize them away, whether all compilers do so is another matter...

---

## Comment 4

> Username: fun4jimmy  
> Created_at: 2016-06-16 08:44:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/15#issuecomment-226424670  

Ok, I understand. Let me know what you decide to do either way.  
  
I'll open a ticket with Microsoft to get them to fix disabling the warning for template functions using `#pragma runtime_checks` so it can be worked around if necessary. I'll add a link to the issue here once it's opened.  
  
Thanks,  
James

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2016-06-16 12:07:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/15#issuecomment-226466446  

Fixed in https://github.com/boostorg/multiprecision/commit/c7e29b02ff696f01e52c0933ff3cf5c3b9bd88fe

---
