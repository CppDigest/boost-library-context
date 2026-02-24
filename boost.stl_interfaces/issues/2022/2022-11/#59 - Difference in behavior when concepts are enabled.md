# #59 - Difference in behavior when concepts are enabled [Closed]

> Username: hgkjshegfskef  
> Created at: 2022-11-10 17:06:04 UTC  
> Updated at: 2022-11-19 01:57:07 UTC  
> Closed at: 2022-11-19 01:57:07 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/59  

Is it expected to have different behavior when using and not using concepts? Perhaps I just don't understand how to use the library well. In this example https://godbolt.org/z/W8bWEnoTr I explicitly request a mutable iterator, and rely on the library to provide `operator->`. The library provides an immutable version of `operator->` when not using concepts. When using concepts, the code builds and works as expected. Any ideas?

---

## Comment 1

> Username: tzlaine  
> Created at: 2022-11-19 01:56:56 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/59#issuecomment-1320743440  

Thanks for reporting this!  Sorry it took me so long to get back to you.  I was at a C++ committee meeting, then I was super sick for a few days.  Thanks especially for the single-file repro!
