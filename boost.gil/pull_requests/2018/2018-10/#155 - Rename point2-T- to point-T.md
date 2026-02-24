# #155 Rename point2<T> to point<T> [Merged]

> Username: mloskot  
> Created at: 2018-10-18 21:26:19 UTC  
> Updated at: 2018-10-19 07:32:27 UTC  
> Merged at: 2018-10-19 07:32:24 UTC  
> Closed at: 2018-10-19 07:32:24 UTC  
> Url: https://github.com/boostorg/gil/pull/155  

Add `point2<T>` alias template for backward compatibility with Boost <=1.68.  
Replace multiple `point_t` aliases of `point<ptrdiff_t>` with single defined in `point.hpp`. The `point_t` is common used to represent dimensions.  
Replace many uses of `point<ptrdiff_t>` with `point_t`.  
  
Apply reformatting around `point2` changes to respect the line length limit.  
  
### References  
  
Follows up discussion in #154  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-10-18 21:32:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/155#pullrequestreview-166304766  

📁 example/mandelbrot.cpp

```diff
  38 |         // (actually make y -1.0..2 so it is asymmetric, so we can verify some view factory methods)
  40 |-         double t=get_num_iter(point2<double>(p.x/(double)_img_size.x*3-2, p.y/(double)_img_size.y*3-1.0f));//1.5f));
  39 |+         double t=get_num_iter(point<double>(p.x/(double)_img_size.x*3-2, p.y/(double)_img_size.y*3-1.0f));//1.5f));
```

> Username: stefanseefeld  
> Created_at: 2018-10-18 21:32:36 UTC  
> Url: https://github.com/boostorg/gil/pull/155#discussion_r226473064  

Any idea why we are not using the previously defined `point_t` here ? (And similarly further down in the same struct)

> Username: mloskot  
> Created_at: 2018-10-18 21:50:36 UTC  
> Url: https://github.com/boostorg/gil/pull/155#discussion_r226477380  

`point_t` is `point<ptrdiff_t>` not for double.   
  
BTW, There may be still numerous places left where the latter is used, waiting to  be replaced with the former.


---
