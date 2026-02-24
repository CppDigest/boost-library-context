# #742 Fix strided_iterator tag [Merged]

> Username: jszuppe  
> Created at: 2017-09-10 10:09:44 UTC  
> Updated at: 2017-09-12 03:22:48 UTC  
> Merged at: 2017-09-12 03:22:47 UTC  
> Closed at: 2017-09-12 03:22:47 UTC  
> Url: https://github.com/boostorg/compute/pull/742  

Using `std::iterator_traits<>` to get `iterator_catagory` prevents from converting STL iterator tag to Boost tag.  
  
Fixes #741

---

## Review 1 [Commented]

> Username: kylelutz  
> Created_at: 2017-09-10 21:11:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compute/pull/742#pullrequestreview-61694697  

📁 include/boost/compute/iterator/strided_iterator.hpp

```diff
  16 | 
  17 | #include <boost/config.hpp>
  18 |+ #include <boost/static_assert.hpp>
```

> Username: kylelutz  
> Created_at: 2017-09-10 21:11:43 UTC  
> Updated_at: 2017-09-10 21:26:04 UTC  
> Url: https://github.com/boostorg/compute/pull/742#discussion_r137960207  

Doesn't look like this is used here, just included for the test?

> Username: jszuppe  
> Created_at: 2017-09-10 21:19:32 UTC  
> Updated_at: 2017-09-10 21:26:04 UTC  
> Url: https://github.com/boostorg/compute/pull/742#discussion_r137960391  

Oh, sorry. I meant to include that in the test. I'll fix it in a moment.


---

## Comment 2

> Username: kylelutz  
> Created_at: 2017-09-10 21:12:15 UTC  
> Url: https://github.com/boostorg/compute/pull/742#issuecomment-328372342  

Looks good, left one comment. Thanks!

---

## Comment 3

> Username: jszuppe  
> Created_at: 2017-09-10 21:21:29 UTC  
> Url: https://github.com/boostorg/compute/pull/742#issuecomment-328372904  

@kylelutz Do you have any idea why Boost.Compute's builds have such a bit delay on Travis CI? I have Travis CI activated for my fork and it always starts immediately: https://travis-ci.org/jszuppe/compute/builds/273862336

---

## Comment 4

> Username: kylelutz  
> Created_at: 2017-09-10 21:23:43 UTC  
> Url: https://github.com/boostorg/compute/pull/742#issuecomment-328373023  

@jszuppe Yeah, that's really strange. AFAICT, they both have the same `.travis.yml` and shouldn't really have any differences. Though I wonder if there's some org-wide throttling that queues us behind other `boostorg` builds?

---

## Comment 5

> Username: jszuppe  
> Created_at: 2017-09-10 21:38:25 UTC  
> Url: https://github.com/boostorg/compute/pull/742#issuecomment-328373812  

I updated the merge-request. You can check build on Travis CI here: https://travis-ci.org/jszuppe/compute/builds/273955520, just in case Boost.Compute build does not start soon.  
  
> Though I wonder if there's some org-wide throttling that queues us behind other boostorg builds?  
  
Maybe, but when I go into boostorg page it does not seem like there are a lot of builds. Also it looks like [this](https://travis-ci.org/boostorg/hana/builds/273421992) build in Hana lib was started just an 2 hours ago, and it has been started.

---

## Comment 6

> Username: coveralls  
> Created_at: 2017-09-11 22:06:30 UTC  
> Url: https://github.com/boostorg/compute/pull/742#issuecomment-328672618  

[![Coverage Status](https://coveralls.io/builds/13223314/badge)](https://coveralls.io/builds/13223314)  
  
Changes Unknown when pulling **fe23b07b7c883fac2a5151439ef20b78a791073f on jszuppe:fix_strided_iter_tag** into ** on boostorg:develop**.

---
