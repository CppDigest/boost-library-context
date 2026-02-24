# #20 Add try catch for close_impl() [Closed]

> Username: ya1gaurav  
> Created at: 2015-11-03 06:17:00 UTC  
> Updated at: 2016-12-06 19:35:11 UTC  
> Closed at: 2016-12-06 19:35:11 UTC  
> Url: https://github.com/boostorg/iostreams/pull/20  

close_impl() throw as below:    
  if (!success && throw_)  
                throw_system_failure("failed closing file");  
Added try catch block at required places.

---

## Comment 1

> Username: eldiener  
> Created_at: 2016-12-04 03:09:44 UTC  
> Url: https://github.com/boostorg/iostreams/pull/20#issuecomment-264681473  

I do not think your PR is correct. The close_impl call's second parameter tells it if it can 'throw' or not, and masking the exception when you tell close_impl that it can throw goes against the design of the function.

---
