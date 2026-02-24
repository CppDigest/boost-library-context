# #1 Drop unused typedef in boost::xpressive::detail::make_assert_word [Merged]

> Username: pmachata  
> Created at: 2014-05-20 22:36:29 UTC  
> Updated at: 2014-08-15 14:39:41 UTC  
> Merged at: 2014-05-21 19:39:13 UTC  
> Closed at: 2014-05-21 19:39:13 UTC  
> Url: https://github.com/boostorg/xpressive/pull/1  

This takes care of a warning that GCC post 4.8 emits.  The typedef indeed isn't used in the function and doesn't seem to serve a function (sometimes these typedefs are there in lieu of static asserts--this is not the case).

---

## Comment 1

> Username: ericniebler  
> Created_at: 2014-05-21 19:39:15 UTC  
> Url: https://github.com/boostorg/xpressive/pull/1#issuecomment-43804086  

Thanks!

---
