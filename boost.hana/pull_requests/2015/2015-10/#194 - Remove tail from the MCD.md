# #194 [Iterable] Remove tail from the MCD [Merged]

> Username: ldionne  
> Created at: 2015-10-27 12:59:11 UTC  
> Updated at: 2015-11-11 20:59:31 UTC  
> Merged at: 2015-11-11 20:59:23 UTC  
> Closed at: 2015-11-11 20:59:23 UTC  
> Url: https://github.com/boostorg/hana/pull/194  

This pull request tries to address #158 by removing `tail`. Things left to do/consider:  
- [x] Should we redefine the MCD in terms of `drop_front` or `drop_front_exactly`?  
     **Resolution: use `drop_front`**  
     Defining `drop_front` in terms of `drop_front_exactly` is very inefficient, because we have to basically `drop_front_exactly(xs, 1)`, check whether that's empty and drop one more if it's not. We do this recursively, which is awful. On the other hand, it's possible to define `drop_front_exactly` in terms of `drop_front` in an efficient way. First, it's possible to set `drop_front_exactly = drop_front`, because it's invalid to call `drop_front_exactly` with a too large `n`. With that definition, it would silently fail if `n` was too large, which is not awesome, but acceptable. However, it is possible to check whether `drop_front_exactly(xs, n)` should fail in a relatively efficient way, by simply checking whether `drop_front(xs, n-1)` is empty.  
- [x] Should we remove `tail` entirely? Probably, since it is superseded by `drop_front(xs)`.  
    **Resolution: Yes, screw `tail`**

---
