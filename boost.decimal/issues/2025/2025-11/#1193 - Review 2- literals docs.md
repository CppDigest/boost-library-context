# #1193 - Review 2: literals docs [Closed]

> Username: jzmaddock  
> Created at: 2025-11-04 18:43:04 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-12 18:21:37 UTC  
> Url: https://github.com/boostorg/decimal/issues/1193  

>The example on literals [3] hasn't updated the using namespace  
clause (should be boost::decimal::literals rather than  
boost::decimal). You might find it useful to build and run the  
examples on CI, and include the code in the docs by using asciidoc  
tagged regions  
  
I would echo the call to make sure that examples are built and run: it saves a lot of issues.

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-05 09:50:52 UTC  
> Url: https://github.com/boostorg/decimal/issues/1193#issuecomment-3490232226  

We do run everything that's in the examples folder: https://github.com/cppalliance/decimal/blob/develop/test/Jamfile#L190. I like the suggestion someone had of linking to the example rather than embedding it so we don't have this issue with potential bifurcation
