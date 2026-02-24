# #235 Fix range back() function [Closed]

> Username: sdebionne  
> Created at: 2015-02-27 16:11:22 UTC  
> Updated at: 2015-03-02 09:51:53 UTC  
> Closed at: 2015-03-02 09:39:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/235  

`*(--boost::end(rng));` is not valid for range with raw pointer as iterator (the compiler complains that `'--' needs l-value`. Since the range is supposed to be BidirectionalRange, use `*rbegin()` instead. To illustrate the issue, the following code doesn't not compile:  
  
```  
typedef boost::array<double, 2> point_t;  
typedef boost::iterator_range<point_t*> linestring_t;  
  
linestring_t l;  
  
point_t back = *(--boost::end(l));  
```  
  
NOTE:  `*(--boost::end(rng));` is used in other places (e.g. point_in_geopetry.hpp:197) and should probably use geometry::back instead. If the fix is OK for you I could try to track and fix them.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-02-27 17:08:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/235#issuecomment-76430502  

Thanks for catching this!  
  
To avoid the creation of an object of reverse_iterator we could manually implement it as:  
  
```  
typename boost::range_iterator<BidirectionalRange>::type  
    it = boost::end(rng);  
return *(--it);  
```  
  
though I'm not sure if this is needed, your version is shorter and elegant. Thoughts?  
  
Of course, feel free to track and fix those places.  
  
P.S. Please add a tag at the begin of the commit message indicating a part of a library. Such name may be a name of a directory or namespace. In the case of the above commit it could be [util] or [range]. In the case of point_in_geometry it'd be [algorithms], etc.

---

## Comment 2

> Username: sdebionne  
> Created_at: 2015-03-02 08:56:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/235#issuecomment-76677095  

It turns out that point_in_geometry.hpp was the only file that uses `*(--boost::end(rng));`. I have added the modified file to the PR.  
  
I would expect the reverse_iterator construction to be completely inlined and optimized away by the compiler but would not put my fingers on it.  
  
How shall I amend my previous commit to add the [range] prefix ? Is it OK for this time or should I "rewrite history" (since the commit has already published to github) ?

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-03-02 09:04:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/235#issuecomment-76678040  

Since the changes you have made are really simple, I would suggest (at least this is what I would do), to close this PR and create another one with the proper commit messages.  
Also please use the `[algorithms]` tag in front of `[range]` or `[within]`. Something like:  
  
```  
[algorithms][range] Fix range back  
*(--boost::end(rng)); is not valid for range with pointer as iterators.  
since the range is supposed to be BidirectionalRange, use rbegin  
instead.  
```

---

## Comment 4

> Username: sdebionne  
> Created_at: 2015-03-02 09:39:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/235#issuecomment-76682472  

Allright, I'm closing this PR and will reopen a new one.

---
