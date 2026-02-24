# #120 - More documentation for interruption is required [Closed]

> Username: akrzemi1  
> Created at: 2023-09-24 18:54:05 UTC  
> Updated at: 2023-10-16 15:04:18 UTC  
> Closed at: 2023-10-16 15:04:18 UTC  
> Url: https://github.com/boostorg/cobalt/issues/120  

The notions of "interrupt", "cancel" and "detach" are not documented enough. After reading the doc, I do not know how I should use interuption and if I even should use it.  
  
I read in one place that an interruption causes not completed awaitables to be detached. Can I detach a `promise` or a `generator` this way? But I see nothing on the `generator`'s or `promise`'s documentation to confirm it.   
  
Do I have to do anything in my coroutine to enable the interruption?  
  
HOw is cancellation different?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-09-28 03:41:01 UTC  
> Url: https://github.com/boostorg/cobalt/issues/120#issuecomment-1738388855  

Interrupt is akin to a semi-public implementation detail. It's meant to make select work lossless.  
  
What gets detached is the co_await itself, not the awaited thing.
