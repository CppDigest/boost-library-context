# #213 - Document operators defined for concepts. [Closed]

> Username: ricejasonf  
> Created at: 2015-11-21 01:16:27 UTC  
> Updated at: 2016-02-05 21:32:59 UTC  
> Closed at: 2016-02-05 21:32:59 UTC  
> Url: https://github.com/boostorg/hana/issues/213  

In `include/boost/hana/detail/operators/*.hpp` there are operators defined for concepts that aren't making it into the documentation. They don't show up in the search bar, and I was unable to find them in side-menu including `Details -> adl`.  
  
One example is the `operator|` for the Monad `chain` function.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-11-21 17:23:49 UTC  
> Url: https://github.com/boostorg/hana/issues/213#issuecomment-158663331  

Actually, these operators are defined per-datatype. Indeed, they are not provided by the concept, but by the container. For example, see [hana::tuple's documentation](http://boostorg.github.io/hana/structboost_1_1hana_1_1tuple.html). There's a section called _provided operators_.

---

## Comment 2

> Username: ricejasonf  
> Created at: 2015-11-21 18:02:08 UTC  
> Url: https://github.com/boostorg/hana/issues/213#issuecomment-158667647  

Aha. I noticed some operators show up in the side menu and the search bar but not others.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-11-21 18:04:42 UTC  
> Url: https://github.com/boostorg/hana/issues/213#issuecomment-158667779  

Which ones?  
  
You know what; I think we can do better by actually documenting `operator|` & friends with Doxygen for each container. Something like  
  
``` c++  
#ifdef BOOST_HANA_DOXYGEN_INVOKED  
//! Equivalent to `hana::chain`.  
template <typename ...T, typename F>  
constexpr auto operator|(tuple<T...>, F);  
#endif  
```

---

## Comment 4

> Username: ldionne  
> Created at: 2016-02-05 21:32:59 UTC  
> Url: https://github.com/boostorg/hana/issues/213#issuecomment-180563623  

This is fixed by PR #250.
