# #283 roots: Fix fma_workaround [Merged]

> Username: neheb  
> Created at: 2019-12-20 01:46:57 UTC  
> Updated at: 2019-12-20 16:34:58 UTC  
> Merged at: 2019-12-20 15:53:04 UTC  
> Closed at: 2019-12-20 15:53:04 UTC  
> Url: https://github.com/boostorg/math/pull/283  

fma takes three parameters, not one.  
  
Signed-off-by: Rosen Penev <rosenp@gmail.com>

---

## Comment 1

> Username: neheb  
> Created_at: 2019-12-20 02:04:10 UTC  
> Url: https://github.com/boostorg/math/pull/283#issuecomment-567755777  

A second problem showed up. Because fma_workaround was not marked inline, it was generating multiple functions, per the template and causing linker errors. Fixed that as well.

---

## Comment 2

> Username: neheb  
> Created_at: 2019-12-20 15:10:32 UTC  
> Url: https://github.com/boostorg/math/pull/283#issuecomment-567957461  

Seems buildbot is stuck.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2019-12-20 15:52:54 UTC  
> Url: https://github.com/boostorg/math/pull/283#issuecomment-567972165  

Many thanks for the patch - appveyor (msvc tests) is irrelevant for this change, so merging.

---
