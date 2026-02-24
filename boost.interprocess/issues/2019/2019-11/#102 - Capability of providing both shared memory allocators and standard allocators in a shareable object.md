# #102 - Capability of providing both shared memory allocators and standard allocators in a shareable object. [Closed]

> Username: haydenmcp  
> Created at: 2019-11-15 21:46:38 UTC  
> Updated at: 2021-04-21 12:33:55 UTC  
> Closed at: 2021-04-21 12:33:55 UTC  
> Url: https://github.com/boostorg/interprocess/issues/102  

To whom it may concern,  
  
I'm currently implementing a system that takes advantage of this awesome library. Implementing shared objects has proven to be decently simple. I'm really enjoying it. However, I'd like to implement an object that can be stored in both shared memory and traditional memory by simply providing an optional allocator as the final param in the constructor.  
  
The part that's proven difficult is compatibility of different allocator types with the interprocess allocator. In other words, I can pass the interprocess allocator as a param and things work well (object is allocated in shared memory). However, I can't pass a different allocator type in its place to use traditional object allocation when the object should be stored elsewhere. This could certainly be the result of inexperience with allocators in C++.  
  
Do you know of a way to achieve this goal that provides simple objects that can be maintained in one class? Up to this point I basically create two objects that are logically identical; one that's allocated in shared memory and one that's not.   
  
Thank you very much for your time and help and for the excellent interprocess library!  
  
Warmest regards,  
Hayden McParlane

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-04-21 12:33:55 UTC  
> Url: https://github.com/boostorg/interprocess/issues/102#issuecomment-824024281  

Sorry Hayden for not replying to this issue. Using standard or shared memory allocators is not feasible as shared memory allocators need special pointer types. The only way I've found is to template your class on the allocator type, which is very intrusive, and few times is feasible.
