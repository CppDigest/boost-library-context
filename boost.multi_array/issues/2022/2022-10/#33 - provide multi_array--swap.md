# #33 - provide multi_array::swap [Open]

> Username: mborgerding  
> Created at: 2022-10-10 13:53:13 UTC  
> Updated at: 2022-10-10 13:53:13 UTC  
> Url: https://github.com/boostorg/multi_array/issues/33  

... or at least make its members protected to allow a subclass to do so.  
  
`swap` would be substantially the same as the current `resize` method.  
  
Background: I use a subclass of `multi_array` in order to resize during assignment  to get around [the most dagnerous multi_array pitfall](http://coliru.stacked-crooked.com/a/53484c30d4cbc065). This works, but I'd also like to fix another longstanding inefficiency ( #24 ) while I'm at it. Unfortunately the `private` data members make it impossible to fix in my subclass (barring a type-punning hack).
