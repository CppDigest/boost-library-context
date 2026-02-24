# #177 Fix resize bug with vector::assign() [Merged]

> Username: kylelutz  
> Created at: 2014-07-08 04:57:10 UTC  
> Updated at: 2014-07-10 05:09:51 UTC  
> Merged at: 2014-07-10 05:09:40 UTC  
> Closed at: 2014-07-10 05:09:40 UTC  
> Url: https://github.com/boostorg/compute/pull/177  

This fixes a bug in which vector::assign() would not resize  
itself to accommodate the assigned values. Now the behavior  
matches that of std::vector.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-07-08 05:23:34 UTC  
> Url: https://github.com/boostorg/compute/pull/177#issuecomment-48273236  

[![Coverage Status](https://coveralls.io/builds/942894/badge)](https://coveralls.io/builds/942894)  
  
Coverage increased (+0.01%) when pulling **4a1b3edf481349462abd2d072994126ba187b29e on fix-vector-assign** into **89089576ab6e02d589ccd4432f610bfc80916003 on develop**.

---
