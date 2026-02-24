# #32 Conform to std::pointer_traits requirements [Merged]

> Username: treh  
> Created at: 2017-01-13 15:08:05 UTC  
> Updated at: 2017-01-19 22:16:30 UTC  
> Merged at: 2017-01-19 22:15:56 UTC  
> Closed at: 2017-01-19 22:15:56 UTC  
> Url: https://github.com/boostorg/interprocess/pull/32  

offset_ptr<T, ...>::rebind<U> must be offset_ptr<U, ...> directly  
  
to avoid breaking older pointer_traits implementations (like in Visual Studio 2015),  
'other' is added as typedef so offset_ptr<T, ...>::rebind< U >::other still works

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2017-01-19 22:16:30 UTC  
> Url: https://github.com/boostorg/interprocess/pull/32#issuecomment-273916198  

Many thanks for the patch, great compatibility trick.

---
