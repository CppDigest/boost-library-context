# #324 Set pipe size issue322 [Closed]

> Username: SilverPlate3  
> Created at: 2023-07-01 06:33:14 UTC  
> Updated at: 2025-02-26 14:58:59 UTC  
> Closed at: 2025-02-26 14:58:59 UTC  
> Url: https://github.com/boostorg/process/pull/324  

Possible Solution to https://github.com/boostorg/process/issues/322.  
Including unit tests.  
  
Please let me know if:  
  
1. You prefer the API to be part of the bp::opstream/bp::ipstream. So users won't need to go into ip.pipe.set_pipe_capacity().  
2. Would you like me to add it to the doc's?  
3. When could it be pushed in?

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2023-08-14 09:49:46 UTC  
> Url: https://github.com/boostorg/process/pull/324#issuecomment-1677021572  

I don't think this should be part of the API. It's linux only , so you can just use fcntl manually. Its trivial, really.

---
