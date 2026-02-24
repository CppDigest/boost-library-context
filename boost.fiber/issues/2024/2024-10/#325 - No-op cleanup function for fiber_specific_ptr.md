# #325 - No-op cleanup function for fiber_specific_ptr [Open]

> Username: beojan  
> Created at: 2024-10-17 22:45:37 UTC  
> Updated at: 2024-10-17 22:45:37 UTC  
> Url: https://github.com/boostorg/fiber/issues/325  

I have a use case where I need a fiber_specific_ptr that is just an observer, and should not delete the object when the fiber completes. A built-in no-op cleanup function (perhaps used if the single argument constructor is used passing in a nullptr) would help with this.
