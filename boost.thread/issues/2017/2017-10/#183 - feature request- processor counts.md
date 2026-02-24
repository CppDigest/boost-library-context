# #183 - feature request: processor counts [Closed]

> Username: jmichae3  
> Created at: 2017-10-17 01:57:35 UTC  
> Updated at: 2017-10-17 05:40:37 UTC  
> Closed at: 2017-10-17 05:38:29 UTC  
> Url: https://github.com/boostorg/thread/issues/183  

std::thread lacks GetActiveProcessorCount() and GetMaximumProcessorCount() or equivalent methods to find out how many threads or processors there are in the computer. without that information it's too easy to overload the box with too many threads and it gets really slow.  
group processor counts are probably needed also since that is a concern on big iron.

---

## Comment 1

> Username: viboes  
> Created at: 2017-10-17 05:38:29 UTC  
> Url: https://github.com/boostorg/thread/issues/183#issuecomment-337123235  

Why are you creating those issues in this repo?
