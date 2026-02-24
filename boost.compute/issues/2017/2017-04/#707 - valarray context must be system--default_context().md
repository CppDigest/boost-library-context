# #707 - valarray context must be system::default_context() [Open]

> Username: jszuppe  
> Created at: 2017-04-18 19:22:00 UTC  
> Updated at: 2017-04-18 19:22:00 UTC  
> Url: https://github.com/boostorg/compute/issues/707  

`valarray` needs some fixing. Currently, it's possible to create a `valarray` object with buffer in some custom context that is not the context of `system::default_queue()` which is used in every method... And that breaks the code.  
  
Since `valarray` is not a popular feature, I think we should make it work only in `system::default_context()`. Otherwise, we have two other options:   
1. force (almost) every operator and method to create a queue based on context of underlying buffer (`m_buffer`) and work synchronously,  
2. keep `command_queue` object in `valarray`.
