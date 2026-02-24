# #63 - Should LEAF provide allocating context for simple multi-thread error handling? [Closed]

> Username: Snarpix  
> Created at: 2023-09-27 13:46:55 UTC  
> Updated at: 2024-01-11 02:17:10 UTC  
> Closed at: 2024-01-11 02:17:10 UTC  
> Url: https://github.com/boostorg/leaf/issues/63  

If I want to run user-provided function on my thread pool and return result, currently I have to ask user to provide error_handlers to use leaf::make_shared_context to allocate memory for error objects. This is not very convenient.  
  
Other option is that I can force user to handle transportation of leaf::result between threads. In that case user will call make_shared_context, but that makes use of my library inconvenient.  
  
In simplest case I just want to transport all errors between threads by allocating memory for error objects and allow user code to handle them. Maybe LEAF should provide such context?

---

## Comment 1

> Username: zajo  
> Created at: 2023-09-27 19:02:56 UTC  
> Url: https://github.com/boostorg/leaf/issues/63#issuecomment-1737929669  

Yes this is on my list of things to do.

---

## Comment 2

> Username: zajo  
> Created at: 2023-12-10 02:30:35 UTC  
> Url: https://github.com/boostorg/leaf/issues/63#issuecomment-1848837856  

I have not updated the documentation yet, but this is pretty much done, check it out in the feature/dynamic_capture branch.  
  
I've decided to do away with the ability of exceptions to carry a capture directly, now if you want to capture some error objects and carry them around, you put them in a `leaf::result<T>`. There is a new error object type called `dynamic_capture`, so:  
  
```  
auto r = try_handle_some(  
    []  
    {  
        ....  
    },  
    []( leaf::dynamic_capture const & cap )  
    {  
        return cap;  
    } );  
```  
  
And now `r` has everything that `dynamic_capture` captured. The caveat is that it only captures things for which there isn't an active handler currently.  
  
Of course now `verbose_diagnostic_info` is implemented in terms of `dynamic_capture` internally.  
  
Feedback welcome.

---

## Comment 3

> Username: Snarpix  
> Created at: 2023-12-16 08:19:30 UTC  
> Updated at: 2023-12-16 08:31:04 UTC  
> Url: https://github.com/boostorg/leaf/issues/63#issuecomment-1858759291  

Thanks, that looks amazing!  
  
Only one note:  
It will be convenient if there will be a function in leaf that wraps around try_handle_some with dynamic_capture.   
It's a common pattern to just catch everything.  
Something like:  
```  
      // Pseudocode  
      std::shared_ptr<leaf::polymorphic_context> ctx = leaf::make_shared_context(leaf::dynamic_capture_ctx{});  
      return leaf::capture(ctx, &task);  
```  
But for dynamic_capture:  
```  
      return leaf::dynamic_capture_all(&task);  
```  
  
Thank you, it's a really cool feature!

---

## Comment 4

> Username: zajo  
> Created at: 2024-01-07 02:00:34 UTC  
> Url: https://github.com/boostorg/leaf/issues/63#issuecomment-1879913297  

@Snarpix see current `develop` branch. I deleted the dynamic_capture functionalety, replaced by try_capture_all which works the way you suggested.
