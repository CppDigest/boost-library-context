# #145 - Consider defaulting template parameters for exception_policy class [Open]

> Username: correaa  
> Created at: 2025-10-03 21:39:43 UTC  
> Updated at: 2025-10-03 21:39:43 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/145  

From the documentation  
  
```cpp  
using logging_exception_policy = exception_policy<  
    log_runtime_exception,    // arithmetic error  
    ignore_exception,         // implementation defined behavior  
    ignore_exception,         // undefined behavior  
    ignore_exception          // uninitialized value  
>;  
```  
  
It would make it less verbose to default the last three template parameters, in which case the example will look like this:  
  
```  
using logging_exception_policy = exception_policy<  
    log_runtime_exception,    // arithmetic error  
>;  
```  
  
making use cases like this simpler: https://godbolt.org/z/Yqa51dE7b
