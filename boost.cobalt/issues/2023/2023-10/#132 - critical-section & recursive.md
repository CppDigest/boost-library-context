# #132 - critical-section & recursive [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-10-11 01:01:34 UTC  
> Updated at: 2024-05-02 00:51:44 UTC  
> Closed at: 2024-05-02 00:51:44 UTC  
> Url: https://github.com/boostorg/cobalt/issues/132  

Thanks to `source_location` this library could provide a critical section like this:  
  
```cpp  
{  
   auto sec = co_await critical_section();  
   // this is limited to one execution PER executor  
}  
```  
  
Interestingly, because it's unique per executor we can then use the critical section to create a recursive token (i.e. one that calls `io_context::run_one` until it gets a result) which then in turn can be used in synchronous callbacks (see #130 for a use-case).   
  
Additionally, we'd probably still need to track the overall recursion depth of the call to `run_one`, in case a user applies this pattern very enthusiastically.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-05-02 00:51:44 UTC  
> Url: https://github.com/boostorg/cobalt/issues/132#issuecomment-2089352699  

Not happening, concept is too complicated.
