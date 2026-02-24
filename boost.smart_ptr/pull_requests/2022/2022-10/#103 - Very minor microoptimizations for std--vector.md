# #103 Very minor microoptimizations for std::vector [Closed]

> Username: SiliconA-Z  
> Created at: 2022-10-19 17:36:18 UTC  
> Updated at: 2022-10-20 09:55:39 UTC  
> Closed at: 2022-10-20 09:55:39 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/103  



---

## Comment 1

> Username: pdimov  
> Created_at: 2022-10-20 09:55:39 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/103#issuecomment-1285253462  

The lack of `reserve` is deliberate, and there's no need to change `push_back` to `emplace_back` (which isn't in C++03.)

---
