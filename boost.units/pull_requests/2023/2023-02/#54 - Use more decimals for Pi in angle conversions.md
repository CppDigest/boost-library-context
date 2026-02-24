# #54 Use more decimals for Pi in angle conversions [Open]

> Username: hernando  
> Created at: 2023-02-27 16:14:16 UTC  
> Updated at: 2023-02-28 12:22:08 UTC  
> Url: https://github.com/boostorg/units/pull/54  

The previous value had twelve signficant digits which is quite below the maximum precision of double floating numbers.  
In this change the value returned by Python for the expression math.pi * 2 is used. This value is the same as the one returned by std::cos(-1) * 2 with 16 significant digits.

---

## Comment 1

> Username: hernando  
> Created_at: 2023-02-27 16:16:05 UTC  
> Url: https://github.com/boostorg/units/pull/54#issuecomment-1446622457  

I didn't try running the tests because the set up doesn't look straight forward and the tolerances used for comparison in test_trig.cpp are rather large. I didn't find any mention of degree or gradian elsewhere in the tests.

---
