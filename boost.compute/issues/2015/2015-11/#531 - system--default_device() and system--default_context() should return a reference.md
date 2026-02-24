# #531 - system::default_device() and system::default_context() should return a reference [Open]

> Username: pisto  
> Created at: 2015-11-04 21:08:04 UTC  
> Updated at: 2017-03-21 04:14:48 UTC  
> Url: https://github.com/boostorg/compute/issues/531  

Imo. No need to create copies and do `clRetain()` every time. And for consistency with `system::default_queue()`  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-11-11 05:17:07 UTC  
> Url: https://github.com/boostorg/compute/issues/531#issuecomment-155670001  

Interesting, I'll look into this.
