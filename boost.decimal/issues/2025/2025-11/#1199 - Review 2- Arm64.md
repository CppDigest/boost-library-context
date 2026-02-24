# #1199 - Review 2: Arm64 [Closed]

> Username: jzmaddock  
> Created at: 2025-11-04 18:47:43 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-05 10:05:03 UTC  
> Url: https://github.com/boostorg/decimal/issues/1199  

I think you may have fixed this one already?  
  
>On Arm64: …boost\decimal\detail\u256.hpp(691): error C3861: '_addcarry_u64': identifier not found

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-05 10:05:03 UTC  
> Url: https://github.com/boostorg/decimal/issues/1199#issuecomment-3490300987  

Fixed by: https://github.com/cppalliance/decimal/pull/1129
