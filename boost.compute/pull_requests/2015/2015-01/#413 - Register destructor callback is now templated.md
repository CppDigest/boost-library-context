# #413 Register destructor callback is now templated [Merged]

> Username: bkchr  
> Created at: 2015-01-16 15:44:05 UTC  
> Updated at: 2015-01-21 03:57:14 UTC  
> Merged at: 2015-01-21 03:57:03 UTC  
> Closed at: 2015-01-21 03:57:03 UTC  
> Url: https://github.com/boostorg/compute/pull/413  

Added a template based register destructor callback function to the memory_object class.

---

## Comment 1

> Username: coveralls  
> Created_at: 2015-01-16 16:00:10 UTC  
> Url: https://github.com/boostorg/compute/pull/413#issuecomment-70274686  

[![Coverage Status](https://coveralls.io/builds/1755123/badge)](https://coveralls.io/builds/1755123)  
  
Coverage increased (+0.01%) when pulling **09d3c44cd7982a9cab3f4c17ccaa924a4912a9f4 on bastiankoe:destructor_callback** into **861a75ae9f05f5bbd25d13120788133a1c9dc886 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-01-17 02:41:42 UTC  
> Updated_at: 2015-01-20 09:20:36 UTC  
> Url: https://github.com/boostorg/compute/pull/413#discussion_r23121990  

Hmm, I don't think this `#include` is necessary (looks like it's just used by the test)?

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-01-17 02:43:32 UTC  
> Url: https://github.com/boostorg/compute/pull/413#issuecomment-70351379  

Just one small issue with the extra include. Also, could you rebase this on `develop` as I just merged your other pull-request which also touched `test_buffer.cpp` and now there's a conflict. Thanks!

---

## Comment 4

> Username: bkchr  
> Created_at: 2015-01-20 08:34:10 UTC  
> Updated_at: 2015-01-20 09:20:36 UTC  
> Url: https://github.com/boostorg/compute/pull/413#discussion_r23208251  

Yeah it's not necessary. I don't know why I put this there, you're right that this belongs to the test.

---

## Comment 5

> Username: coveralls  
> Created_at: 2015-01-20 09:35:57 UTC  
> Url: https://github.com/boostorg/compute/pull/413#issuecomment-70627086  

[![Coverage Status](https://coveralls.io/builds/1772113/badge)](https://coveralls.io/builds/1772113)  
  
Coverage increased (+0.01%) when pulling **1e711e0330b799d311287bcbb7639517a1546afd on bastiankoe:destructor_callback** into **f4bf942a311100c2a1ecd1d14ab3ba1ea53644aa on kylelutz:develop**.

---

## Comment 6

> Username: bkchr  
> Created_at: 2015-01-20 10:05:37 UTC  
> Url: https://github.com/boostorg/compute/pull/413#issuecomment-70630783  

I hope that now everything is okay ;)

---

## Comment 7

> Username: kylelutz  
> Created_at: 2015-01-21 03:57:14 UTC  
> Url: https://github.com/boostorg/compute/pull/413#issuecomment-70780917  

Merged! Thanks!

---
