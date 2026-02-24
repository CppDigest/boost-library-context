# #12 Skip fill_char_pair_vector test on AMD [Merged]

> Username: ddemidov  
> Created at: 2013-05-24 05:04:51 UTC  
> Updated at: 2013-06-24 12:07:36 UTC  
> Merged at: 2013-05-24 05:22:35 UTC  
> Closed at: 2013-05-24 05:22:35 UTC  
> Url: https://github.com/boostorg/compute/pull/12  

The test fails for the same reason that fill_pair_vector does.  
  
See: c085d64a46b315bf9e884283d6b980b2191b72bc  
See: http://devgurus.amd.com/thread/166622  
  
closes: #2

---

## Comment 1

> Username: ddemidov  
> Created_at: 2013-05-24 05:22:52 UTC  
> Updated_at: 2013-05-24 05:23:14 UTC  
> Url: https://github.com/boostorg/compute/pull/12#issuecomment-18387122  

With this, all Boost.compute tests are passing on NVIDIA and AMD (both CPU and GPU) platforms. There are some problems with Intel OpenCL and pocl (http://pocl.sourceforge.net). I think I have an outdated Intel SDK (It fails some vexcl tests as well), and at least some of pocl issues result from unimplemented OpenCL features.  
  
I think #2 may be closed now, but you may want to check the pocl issues yourself (I managed to find a bug in vexcl code this way).

---

## Comment 2

> Username: kylelutz  
> Created_at: 2013-05-24 05:22:57 UTC  
> Url: https://github.com/boostorg/compute/pull/12#issuecomment-18387125  

Merged. Thanks!

---

## Comment 3

> Username: kylelutz  
> Created_at: 2013-05-24 05:28:17 UTC  
> Url: https://github.com/boostorg/compute/pull/12#issuecomment-18387230  

Hmm, I haven't had any problems with Intel and I test it fairly regularly. I'm using version "1.2-3.0.56860" which I think is the latest. I'll look more into pocl.

---

## Comment 4

> Username: ddemidov  
> Created_at: 2013-05-24 05:30:46 UTC  
> Url: https://github.com/boostorg/compute/pull/12#issuecomment-18387272  

Mine Intel SDK has version `2.0.31360`, so its pretty ancient. I'll probably have to install a fresh version manually.

---
