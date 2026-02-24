# #470 Replace use of boost/iterator/detail/enable_if.hpp [Merged]

> Username: pdimov  
> Created at: 2025-01-28 00:28:54 UTC  
> Updated at: 2025-02-06 14:13:35 UTC  
> Merged at: 2025-02-01 17:35:16 UTC  
> Closed at: 2025-02-01 17:35:16 UTC  
> Url: https://github.com/boostorg/python/pull/470  

`boost/iterator/detail/enable_if.hpp` has been deleted. Replace with `boost/type_traits/enable_if.hpp`.

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-01-30 19:17:41 UTC  
> Url: https://github.com/boostorg/python/pull/470#issuecomment-2625368637  

Note that Boost.Python currently fails to build without this PR, so it should be applied sooner rather than later, and preferably merged to master as well.

---

## Comment 2

> Username: OgreTransporter  
> Created_at: 2025-02-01 16:34:32 UTC  
> Url: https://github.com/boostorg/python/pull/470#issuecomment-2629019173  

https://github.com/boostorg/python/blob/4fc3afa3ac1a1edb61a92fccd31d305ba38213f8/include/boost/python/object_operators.hpp#L43  
  
Or use `std::enable_if` instead of `boost::iterators::enable_if`.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-02-01 17:35:07 UTC  
> Url: https://github.com/boostorg/python/pull/470#issuecomment-2629040299  

I'm merging this to unblock the [Boost CI](https://github.com/boostorg/boost/actions/workflows/ci.yml) which fails because Boost.Python fails to build.

---

## Comment 4

> Username: francesco-ballarin  
> Created_at: 2025-02-06 10:15:38 UTC  
> Url: https://github.com/boostorg/python/pull/470#issuecomment-2639392182  

@pdimov can this be backported to `master`? The goal would be for the update to go through to the `master` branch at https://github.com/boostorg/boost.  
  
Thanks,  
Francesco

---

## Comment 5

> Username: pdimov  
> Created_at: 2025-02-06 13:47:08 UTC  
> Url: https://github.com/boostorg/python/pull/470#issuecomment-2639877678  

@stefanseefeld what's your merge to master policy? I don't want to do this without your approval.

---

## Comment 6

> Username: stefanseefeld  
> Created_at: 2025-02-06 14:13:34 UTC  
> Url: https://github.com/boostorg/python/pull/470#issuecomment-2639944580  

@pdimov thanks for asking, let me take care of it right away...done.

---
