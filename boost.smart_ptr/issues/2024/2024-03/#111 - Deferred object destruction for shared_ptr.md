# #111 - Deferred object destruction for shared_ptr [Open]

> Username: ohhmm  
> Created at: 2024-03-01 11:18:00 UTC  
> Updated at: 2024-03-01 12:12:02 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/111  

Once during profiling, I noticed that deletion has major performance impact.  
Since I don't  rely on object under shared_ptr destruction end, I decided to defer the destruction to increase overall performance of getting results.  
My solution looks like this: `void DispatchDispose(std::shared_ptr<void>&&);`: https://github.com/ohhmm/openmind/blob/d2a58112b8e341e8733466e9710feb2ce11d882d/omnn/rt/GC.h#L19  
  
My thought was about that in fact hardly anyone rely on last shared_ptr deletion end to continue code execution. Its only goal is to cleanup resource, but it does not matter when.  
  
I guess that may be useful to have resource cleanup governors for shared_ptr or new kind of smart pointers for this.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-03-01 12:12:01 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/111#issuecomment-1973080598  

If you control how the shared pointers are created, you can use a "deferred deleter" for this, e.g.  
  
https://www.boost.org/doc/libs/1_84_0/libs/smart_ptr/doc/html/smart_ptr.html#techniques_delayed_deallocation
