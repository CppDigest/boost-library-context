# #316 - value_stack performance improvements [Closed]

> Username: sdkrystian  
> Created at: 2020-09-11 16:38:35 UTC  
> Updated at: 2020-09-26 03:08:03 UTC  
> Closed at: 2020-09-26 03:08:03 UTC  
> Url: https://github.com/boostorg/json/issues/316  

There is still quite a bit of performance we can squeeze out of `value_stack`, at the same time reducing binary size. For example, allocations and copying should be done within the `value_stack` `push_object/array/key/string` functions. If the allocation throws, then `~value_stack` will clean up for us, so stack unwinding segments don't need to be generated.  
  
Example implementation here: https://github.com/sdkrystian/json/tree/stack-rework-1

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-26 03:08:03 UTC  
> Url: https://github.com/boostorg/json/issues/316#issuecomment-699286130  

This is no good at all, it breaks encapsulation so we will not be doing it.
