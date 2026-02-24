# #315 Support floating point numbers in thread_safe accumulator [Merged]

> Username: HDembinski  
> Created at: 2021-03-18 12:19:17 UTC  
> Updated at: 2021-04-23 13:04:26 UTC  
> Merged at: 2021-04-23 13:04:23 UTC  
> Closed at: 2021-04-23 13:04:23 UTC  
> Url: https://github.com/boostorg/histogram/pull/315  

This adds a workaround for floating point numbers that I learned from discussion with @emanca.  
  
We further stop inheriting from `std::atomic` for better encapsulation.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2021-03-19 09:05:32 UTC  
> Updated_at: 2021-03-19 09:14:10 UTC  
> Url: https://github.com/boostorg/histogram/pull/315#issuecomment-802670624  

@henryiii @jpivarski Any comments on this?  
  
Not inheriting from std::atomic anymore is technically interface-breaking, but people should not use this accumulator directly, so I think it is acceptable.

---
