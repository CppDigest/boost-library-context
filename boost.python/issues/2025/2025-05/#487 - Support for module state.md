# #487 - Support for module state [Open]

> Username: o01eg  
> Created at: 2025-05-10 16:22:57 UTC  
> Updated at: 2025-05-10 16:22:57 UTC  
> Url: https://github.com/boostorg/python/issues/487  

For multi-phase initialization and sub-interpreters it would be useful to have an access to module state https://docs.python.org/3.9/c-api/module.html#c.PyModule_GetState  
  
It also requires some memory safety workaround and deallocation functions defined.
