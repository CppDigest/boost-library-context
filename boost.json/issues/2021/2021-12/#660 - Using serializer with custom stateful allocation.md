# #660 - Using serializer with custom stateful allocation [Open]

> Username: kamrann  
> Created at: 2021-12-15 17:21:14 UTC  
> Updated at: 2021-12-15 17:34:16 UTC  
> Url: https://github.com/boostorg/json/issues/660  

I have a json `value` which I want to serialize to a string type which uses a custom allocator, and cannot allow the default global allocator to be used during the process either.  
It appears that the `serializer` class uses a stack internally which can allocate. It has a `storage_ptr` member, but I don't see any way to provide my own value for this, and so it is always using the default allocator when the stack needs to reserve more space.  
  
Not sure if this is an oversight, or I'm missing something?  
  
Boost 1.76.0 (appears to be same situation on master)

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-12-15 17:34:16 UTC  
> Url: https://github.com/boostorg/json/issues/660#issuecomment-995012012  

I think, you are correct, we don't have a way to set memory resource for the serializer at the moment.
