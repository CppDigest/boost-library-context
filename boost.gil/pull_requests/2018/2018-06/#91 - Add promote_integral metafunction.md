# #91 Add promote_integral metafunction [Merged]

> Username: mloskot  
> Created at: 2018-06-16 11:47:35 UTC  
> Updated at: 2018-06-16 18:58:34 UTC  
> Merged at: 2018-06-16 15:52:44 UTC  
> Closed at: 2018-06-16 15:52:44 UTC  
> Url: https://github.com/boostorg/gil/pull/91  

### Description  
  
Copied from Boost.Geometry, including original tests, with some non-functional modifications explained in the comments.  
The utility can be used where it is important to avoid integer overflow.  
  
Files copied from Boost.Geometry include:  
  
- [promote_integral.hpp](https://github.com/boostorg/geometry/blob/494045809e1ce639c8aa8d77b78e59564a4eeda3/include/boost/geometry/util/promote_integral.hpp)  
- [test/util/promote_integral.cpp](https://github.com/boostorg/geometry/blob/ba9117b37ee7194dba547bade9c0f1f24c9dea98/test/util/promote_integral.cpp)  
  
### Tasklist  
  
- [x] Add test case(s)  
- [x] Review  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: chhenning  
> Created_at: 2018-06-16 15:53:20 UTC  
> Url: https://github.com/boostorg/gil/pull/91#issuecomment-397821458  

Thanks Mateusz. I understand this request is only adding the functionality but not changing any gil code.

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2018-06-16 15:55:57 UTC  
> Url: https://github.com/boostorg/gil/pull/91#issuecomment-397821613  

Very good. Thanks for working on this !  
I have to admit that I was quite surprised to see all the dependencies this code drags in, but I haven't had the time to review the code to understand why this is needed for something as simple as a integral type promotion template. Perhaps we can simplify that ? Not as urgent as using this new facility to fix the bugs, though ! :-)

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-06-16 16:02:09 UTC  
> Url: https://github.com/boostorg/gil/pull/91#issuecomment-397821993  

@chhenning Yes, this does not add any changes to GIL

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-06-16 16:04:08 UTC  
> Url: https://github.com/boostorg/gil/pull/91#issuecomment-397822108  

@stefanseefeld Indeed. Next, I'm going to replace Boost deps (eg. traits metafunctions) with standard equivalents, wherever possible. I'll continue tonight after kids go to bed :)

---
