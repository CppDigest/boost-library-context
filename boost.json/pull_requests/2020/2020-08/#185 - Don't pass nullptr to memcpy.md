# #185 Don't pass nullptr to memcpy [Closed]

> Username: sdkrystian  
> Created at: 2020-08-22 15:16:00 UTC  
> Updated at: 2020-08-22 21:53:57 UTC  
> Closed at: 2020-08-22 21:53:57 UTC  
> Url: https://github.com/boostorg/json/pull/185  

Thanks @pauldreik :)

---

## Comment 1

> Username: pauldreik  
> Created_at: 2020-08-22 15:35:45 UTC  
> Url: https://github.com/boostorg/json/pull/185#issuecomment-678655042  

You're welcome! Looks like having fuzzing in CI paid off directly!

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-08-22 15:47:41 UTC  
> Url: https://github.com/boostorg/json/pull/185#issuecomment-678656416  

Not even an hour after merging, the fuzzer is already paying the bills

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-22 15:48:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/185#pullrequestreview-472934614  

📁 include/boost/json/detail/utf8.hpp

```diff
 133 |     {
 134 |-         if(! needed())
 134 |+         if(BOOST_JSON_UNLIKELY(! needed()))
```

> Username: vinniefalco  
> Created_at: 2020-08-22 15:48:50 UTC  
> Updated_at: 2020-08-22 20:33:27 UTC  
> Url: https://github.com/boostorg/json/pull/185#discussion_r475103165  

No: `! needed()`  
Yes: `needed() == 0`


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-22 15:49:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/185#pullrequestreview-472934634  

📁 include/boost/json/detail/utf8.hpp

```diff
 142 |-         std::memcpy(seq_ + size_, p, remain);
 143 |-         size_ += static_cast<uint8_t>(remain);
 142 |+         if(BOOST_JSON_LIKELY(remain))
```

> Username: vinniefalco  
> Created_at: 2020-08-22 15:49:06 UTC  
> Updated_at: 2020-08-22 20:33:27 UTC  
> Url: https://github.com/boostorg/json/pull/185#discussion_r475103188  

No: `remain`  
Yes: `remain > 0`


---
