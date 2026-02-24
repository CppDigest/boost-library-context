# #147 [algorithms][for_each] fix bug with for_each_segment for open geometries [Merged]

> Username: mkaravel  
> Created at: 2014-09-30 14:11:06 UTC  
> Updated at: 2014-10-20 05:30:05 UTC  
> Merged at: 2014-09-30 15:53:51 UTC  
> Closed at: 2014-09-30 15:53:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/147  

`for_each_segment` was not considering the last implicit segment of open geometries  
The patch in this PR fixes this bug, by considering the `closeable_view` of the range.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-09-30 17:35:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/147#issuecomment-57351339  

Thanks

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-09-30 21:57:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/147#issuecomment-57389685  

Unfortunately it doesn't compile for mutable Geometries and AFAIU the intention was that it should, the previous version compiled.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-10-01 08:39:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/147#issuecomment-57434652  

Yes, it should...

---
