# #33 - Consider using Boost.PFR [Closed]

> Username: akrzemi1  
> Created at: 2023-12-29 21:04:32 UTC  
> Updated at: 2023-12-30 21:28:33 UTC  
> Closed at: 2023-12-30 21:28:33 UTC  
> Url: https://github.com/boostorg/parser/issues/33  

[Boost.PFR](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_pfr.html) allows tuple-like access to aggregate types without the need to use any "ADAPT" macro. In my parser use cases I use aggregates for representing sequences, and would benefit from not having to use those macros.  
  
Downsides:  
* another Boost dependency  
* slower compile times  
* compile errors from Boost.PFR will show up as errors from Boost.Parser.  
  
Feel free to close this issue at any time. This is just a suggestion.

---

## Comment 1

> Username: tzlaine  
> Created at: 2023-12-30 21:28:33 UTC  
> Url: https://github.com/boostorg/parser/issues/33#issuecomment-1872610391  

I already had this idea to make the example from #28 Easier to write, but I couldn't remember the name PFR!  So, thanks for saving me a lot of searching.  I decided to reimplement the bits of it that I needed.
