# #25 Use snprintf instead of sprintf to silent the security warning in Xcode 14 [Closed]

> Username: biodranik  
> Created at: 2022-06-10 21:47:54 UTC  
> Updated at: 2022-06-13 16:26:12 UTC  
> Closed at: 2022-06-13 16:26:12 UTC  
> Url: https://github.com/boostorg/assert/pull/25  



---

## Comment 1

> Username: biodranik  
> Created_at: 2022-06-10 21:49:41 UTC  
> Url: https://github.com/boostorg/assert/pull/25#issuecomment-1152760671  

<img width="959" alt="image" src="https://user-images.githubusercontent.com/170263/173155544-0e55c06e-2de4-4f67-9402-0134716f99fa.png">

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-06-13 16:26:12 UTC  
> Url: https://github.com/boostorg/assert/pull/25#issuecomment-1154129405  

Fixed with https://github.com/boostorg/assert/commit/601ee4ba7ae9d9b532dab5f7e757f4187dce6300 instead of applying directly because not all supported compilers have `snprintf`.

---
