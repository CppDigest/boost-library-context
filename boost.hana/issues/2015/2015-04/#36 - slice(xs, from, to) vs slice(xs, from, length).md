# #36 - slice(xs, from, to) vs slice(xs, from, length) [Closed]

> Username: ldionne  
> Created at: 2015-04-08 21:48:38 UTC  
> Updated at: 2015-04-09 02:29:42 UTC  
> Closed at: 2015-04-09 02:29:42 UTC  
> Url: https://github.com/boostorg/hana/issues/36  

Currently, `slice` takes a start and a one-past-the-end index. Instead, it could take a start index and a length. I'm not sure which one is better. So the question is whether `slice(xs, from, to)` or `slice(xs, from, length)` should be preferred.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-04-08 21:52:39 UTC  
> Updated at: 2015-04-08 21:59:12 UTC  
> Url: https://github.com/boostorg/hana/issues/36#issuecomment-91046767  

The C++ standard library uses iterators, and ranges are usually denoted as `[first, last)`. Also, by looking at the comparison of array slicing in various programming languages ([here](http://en.wikipedia.org/wiki/Comparison_of_programming_languages_%28array%29#Slicing)), the obvious winner for C++ is the `slice(xs, from, to)` notation. Also note that being able to pass an arbitrary sequence of indices would be great. See #37 for that.
