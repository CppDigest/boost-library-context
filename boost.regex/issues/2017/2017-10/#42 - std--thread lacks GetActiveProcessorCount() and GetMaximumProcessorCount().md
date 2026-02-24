# #42 - std::thread lacks GetActiveProcessorCount() and GetMaximumProcessorCount() [Closed]

> Username: jmichae3  
> Created at: 2017-10-14 18:01:35 UTC  
> Updated at: 2017-10-18 18:46:23 UTC  
> Closed at: 2017-10-18 18:46:23 UTC  
> Url: https://github.com/boostorg/regex/issues/42  

std::thread lacks GetActiveProcessorCount() and GetMaximumProcessorCount() or equivalent methods to find out how many threads or processors there are in the computer. without that information it's too easy to overload the box with too many threads and it gets really slow.

---

## Comment 1

> Username: jmichae3  
> Created at: 2017-10-14 18:21:29 UTC  
> Url: https://github.com/boostorg/regex/issues/42#issuecomment-336653877  

you may wish to include group processor counts as well - some vendors require those.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2017-10-15 08:38:58 UTC  
> Url: https://github.com/boostorg/regex/issues/42#issuecomment-336695419  

What has this to do with Boost.Regex?
