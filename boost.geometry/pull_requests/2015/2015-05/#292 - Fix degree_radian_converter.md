# #292 Fix degree_radian_converter [Merged]

> Username: mkaravel  
> Created at: 2015-05-06 09:54:54 UTC  
> Updated at: 2015-05-13 16:32:24 UTC  
> Merged at: 2015-05-13 12:12:24 UTC  
> Closed at: 2015-05-13 12:12:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/292  

In this PR I modify the implementation of `degree_radian_converter` to use conversion constants that are consistent with the coordinate type of the geometry passed to the converter.  
  
This is important for keeping the accuracy of user-defined number types (e.g., `ttmath`).

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-05-06 17:37:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/292#issuecomment-99546495  

If that's really needed then I'm guessing it should be changed consistently in the whole library consistently. Shouldn't we rather replace the constants with functions?  
  
```  
geometry::math::d2r<T>();  
```

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-05-06 18:33:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/292#issuecomment-99565821  

I did a `grep` in `include/boost/geometry` and there are very few places where `math::d2r` and `math::r2d` are used, which is good.  
  
I agree with you. I definitely prefer having functions rather than constants.  
  
Let's wait a bit for other opinions and I would be more than happy to implement `math::d2r<T>()` and `math::r2d<T>()` and change the current use cases within the library with these new functions.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-05-06 18:34:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/292#issuecomment-99566360  

There are also a few use cases in units tests, which I can also change easily.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-05-08 09:30:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/292#issuecomment-100172468  

- I have replaced the constants `math::r2d` and `math::d2r` by functions (see commit 1deb1e2).  
- I have changed the implementation of the `degree_radian_converter` to use these free functions (see commit 30f9805).  
- I have replaced the constants in transform strategies by the corresponding free functions; in these cases I make sure that the type used for the conversion constants is promoted to a floating point number (see commit fbc568e).  
- I have changed all other occurrences (in debug code and units tests) of the constants with the corresponding free functions (see commit 535a704).

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-05-08 11:40:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/292#issuecomment-100204998  

Thanks! I'm ok with this change but let's wait for Barend and the others :)

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2015-05-13 11:23:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/292#issuecomment-101631583  

I'm OK with merging, thanks for this.  
I will (on term) update the projections (on places where similar conversion is done) to make use of this

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2015-05-13 15:59:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/292#discussion_r30246199  

Oops, inline is missing!  
Here and in r2d

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-05-13 16:32:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/292#issuecomment-101738751  

I just created a PR for this: #296. Thanks for spotting this!!!

---
