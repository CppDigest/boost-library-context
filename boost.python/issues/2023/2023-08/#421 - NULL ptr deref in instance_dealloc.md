# #421 - NULL ptr deref in instance_dealloc [Open]

> Username: vient  
> Created at: 2023-08-30 19:23:23 UTC  
> Updated at: 2023-08-30 19:23:23 UTC  
> Url: https://github.com/boostorg/python/issues/421  

Hi, we've recently triggered null pointer deref in [that place](https://github.com/boostorg/python/blob/47d5bc76f69e20625214381c930a2fad5765e2b3/src/object/class.cpp#L337C49-L337C71), right on dynamic_cast. From the first glance code does not look correct, dynamic_cast occurs after explicitly calling destructor on object. In our combination of clang16, gcc13 and python3.11 (I don't know what exactly triggered this) this destructor compiles in such way that vtable pointer is set to 0 in the end, which does not work so well with dynamic_cast.  
  
Using object after destroying it sound like UB anyway, can it be fixed somehow? Using dynamic_cast before calling object destructor?
