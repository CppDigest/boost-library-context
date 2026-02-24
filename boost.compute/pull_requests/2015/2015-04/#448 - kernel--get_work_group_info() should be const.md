# #448 kernel::get_work_group_info() should be const [Merged]

> Username: ddemidov  
> Created at: 2015-04-04 19:06:03 UTC  
> Updated at: 2015-04-04 22:55:19 UTC  
> Merged at: 2015-04-04 22:55:19 UTC  
> Closed at: 2015-04-04 22:55:19 UTC  
> Url: https://github.com/boostorg/compute/pull/448  

Please correct me if I am wrong, but I don't see a reason for `kernel::get_work_group_info()` to be non-const.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-04-04 22:55:10 UTC  
> Url: https://github.com/boostorg/compute/pull/448#issuecomment-89677950  

No, you're right, it should be `const`. Thanks for fixing it!

---
