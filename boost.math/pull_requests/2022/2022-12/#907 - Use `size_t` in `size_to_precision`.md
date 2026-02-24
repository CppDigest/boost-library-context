# #907 Use `size_t` in `size_to_precision` [Merged]

> Username: Flamefire  
> Created at: 2022-12-28 12:51:31 UTC  
> Updated at: 2022-12-29 16:14:45 UTC  
> Merged at: 2022-12-28 18:22:20 UTC  
> Closed at: 2022-12-28 18:22:20 UTC  
> Url: https://github.com/boostorg/math/pull/907  

As the trait is supposed to be called with `sizeof` the type should be `size_t`  
  
This avoids a conversion warning on e.g. GCC until 10.4. See https://godbolt.org/z/6Pq38zYbW  
  
Reported on Slack: https://cpplang.slack.com/archives/C27KZLB0X/p1672180776054099

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-12-28 18:22:15 UTC  
> Url: https://github.com/boostorg/math/pull/907#issuecomment-1366838400  

Looks good to me...

---
