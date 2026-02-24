# #54 - make histogram iterators faster [Closed]

> Username: HDembinski  
> Created at: 2018-07-04 23:26:52 UTC  
> Updated at: 2018-07-15 22:37:25 UTC  
> Closed at: 2018-07-15 22:37:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/54  

the STL copies iterators a lot, assuming they are cheap to copy. the histogram iterators are not cheap to copy, but expensive parts could be handled with lazy initialization. Or we store the fat parts in the histogram, where they could speed up bin access in general.  
  
benchmarks are needed to decide on the design.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-07-12 09:06:28 UTC  
> Url: https://github.com/boostorg/histogram/issues/54#issuecomment-404444177  

The most elegant way is to write a replacement of boost::variant for this case. It is possible to have fast access to the size and shape attributes then even when any_axis is used. Then we could avoid caching this axis type independent data in the iterator, making it cheap to copy.

---

## Comment 2

> Username: HDembinski  
> Created at: 2018-07-15 22:37:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/54#issuecomment-405123657  

For now, use an index cache associated with each histogram axis.
