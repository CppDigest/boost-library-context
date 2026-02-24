# #12 Use Boost.TypeIndex CTTI to implement type info when RTTI is not enabled [Open]

> Username: Lastique  
> Created at: 2018-10-27 21:47:24 UTC  
> Updated at: 2019-01-05 11:26:06 UTC  
> Url: https://github.com/boostorg/statechart/pull/12  

This allows to solve the problem with comparing addresses of global id_provider  
objects, which fail if the objects reside in different modules. Boost.TypeIndex  
solves this problem by comparing specially crafted strings, which are equal  
if the respective types are the same.  
  
Unfortunately, this will likely result in a performance drop as string  
comparison is likely more expensive than comparing pointers. Any performance  
optimizations are better placed in Boost.TypeIndex.  
  
This is the next step after fixing the build in https://github.com/boostorg/statechart/pull/10. I'm posting it as a separate PR as this change is more controversial.  
  
Relates to and fixes https://github.com/boostorg/statechart/issues/9.

---

## Review 1 [Commented]

> Username: jeking3  
> Created_at: 2018-10-28 02:36:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/statechart/pull/12#pullrequestreview-169076476  

📁 include/boost/statechart/detail/rtti_policy.hpp

```diff
  12 | #include <boost/config.hpp> // BOOST_MSVC
  13 | #include <boost/detail/workaround.hpp>
  14 |+ #if !defined(BOOST_STATECHART_USE_NATIVE_RTTI)
```

> Username: jeking3  
> Created_at: 2018-10-28 02:36:33 UTC  
> Updated_at: 2018-10-28 06:55:31 UTC  
> Url: https://github.com/boostorg/statechart/pull/12#discussion_r228732382  

Do we need to worry about `BOOST_NO_RTTI` here?  
  
Is this macro new? - does it need documentation and additional test cases?

> Username: Lastique  
> Created_at: 2018-10-28 07:00:24 UTC  
> Url: https://github.com/boostorg/statechart/pull/12#discussion_r228736466  

This is to avoid including Boost.TypeIndex when not needed. The macro is not new, it is already documented and is tested by *Native tests.


---

## Comment 2

> Username: jeking3  
> Created_at: 2018-10-28 02:37:25 UTC  
> Url: https://github.com/boostorg/statechart/pull/12#issuecomment-433671367  

If you think the combination of this and #10 solve the build issues, rebase this to test that.

---

## Comment 3

> Username: Lastique  
> Created_at: 2018-10-28 07:01:04 UTC  
> Url: https://github.com/boostorg/statechart/pull/12#issuecomment-433681553  

> If you think the combination of this and #10 solve the build issues, rebase this to test that.  
  
Done.

---

## Comment 4

> Username: jeking3  
> Created_at: 2019-01-05 00:15:33 UTC  
> Url: https://github.com/boostorg/statechart/pull/12#issuecomment-451607094  

Looks like we have one build still failing, the OSX one.  Is the failure related or environmental?  It looks like it is a unit test failure.  I'm going to rekick it once to see if it is transient.  
  
Here's the job that failed:  
https://travis-ci.org/boostorg/statechart/jobs/447312868

---

## Comment 5

> Username: Lastique  
> Created_at: 2019-01-05 00:59:58 UTC  
> Url: https://github.com/boostorg/statechart/pull/12#issuecomment-451612681  

> Is the failure related or environmental?  
  
I don't know, I don't have OS X to investigate.

---

## Comment 6

> Username: apolukhin  
> Created_at: 2019-01-05 11:13:40 UTC  
> Url: https://github.com/boostorg/statechart/pull/12#issuecomment-451646696  

That's a very unusual way to use Boost.TypeIndex library. Initially it was designed to hide all the typeid related workarounds under the hood and deal with the symbol visibilities/rtti in different shared objects.  
  
So in my head the PR should drop all the `BOOST_STATECHART_USE_NATIVE_RTTI` related lines,  replace `boost::typeindex::ctti_type_index::type_info_t` with `boost::typeindex::type_index`, replace `&boost::typeindex::ctti_type_index::type_id< MostDerived >().type_info()` with `&boost::typeindex::type_id< MostDerived >()`.  
  
This will shorten the code and provide the most efficient way for typeid comparisons.

---

## Comment 7

> Username: Lastique  
> Created_at: 2019-01-05 11:26:05 UTC  
> Url: https://github.com/boostorg/statechart/pull/12#issuecomment-451647410  

The reason I did it the way I did is because `BOOST_STATECHART_USE_NATIVE_RTTI` is a documented user config macro. Removing it would be a breaking change, and I'm not totally sure how that would affect the library performance.

---
