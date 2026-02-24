# #894 Update tanh_sinh handling of boundaries. [Merged]

> Username: jzmaddock  
> Created at: 2022-12-05 18:28:02 UTC  
> Updated at: 2024-01-24 21:35:29 UTC  
> Merged at: 2023-01-15 12:40:35 UTC  
> Closed at: 2023-01-15 12:40:35 UTC  
> Url: https://github.com/boostorg/math/pull/894  

So that we don't accidentality end up at an end point, even when arithmetic is inexact, and the FP type has no denomrms.  
Fixes: https://github.com/boostorg/math/issues/893

---
