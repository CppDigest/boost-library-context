# #186 [optional] Make the interface closer to that of std::optional [Merged]

> Username: ldionne  
> Created at: 2015-10-08 20:39:02 UTC  
> Updated at: 2015-10-15 15:07:21 UTC  
> Merged at: 2015-10-15 13:32:30 UTC  
> Closed at: 2015-10-15 13:32:30 UTC  
> Url: https://github.com/boostorg/hana/pull/186  

I'm opening a pull request for this instead of merging directly so that people can tell their opinion about this change. I think it is nice to have an interface that is more similar to `boost::optional` and `std::experimental::optional`. Unfortunately, the interface can't be exactly the same because we can't e.g. provide a conversion operator to an `integral_constant` (well we can, but it does not play well with metaprogramming so it's useless; I've tried).  
  
In all cases; even if the interface can't be exactly the same, having at least some amount of overlap is better than none.

---
