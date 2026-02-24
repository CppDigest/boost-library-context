# #12 Mark <regex> unusable for all libstdc++ versions [Merged]

> Username: Lastique  
> Created at: 2014-03-22 16:39:21 UTC  
> Updated at: 2014-07-10 04:55:29 UTC  
> Merged at: 2014-03-22 16:55:11 UTC  
> Closed at: 2014-03-22 16:55:11 UTC  
> Url: https://github.com/boostorg/config/pull/12  

The <regex> header is present but the regex implementation is incomplete and fails with regex_error exceptions in runtime for many valid patterns. So define BOOST_NO_CXX11_HDR_REGEX for it unconditionally.  
  
See: http://thread.gmane.org/gmane.comp.lib.boost.devel/250010

---

## Comment 1

> Username: jwakely  
> Created_at: 2014-03-23 11:39:20 UTC  
> Url: https://github.com/boostorg/config/pull/12#issuecomment-38380047  

`<regex>` is complete in GCC 4.9, please don't mark it unsupported unconditionally, so that when 4.9 is released in a few weeks Boost is already prepared for it

---

## Comment 2

> Username: Lastique  
> Created_at: 2014-03-23 11:49:04 UTC  
> Url: https://github.com/boostorg/config/pull/12#issuecomment-38380230  

When 4.9 is released and tested, we can add a condition for it. For now I'd prefer to leave it defined unconditionally. Of course, the choice is after John. If he prefers to add the condition now, I can do it.

---

## Comment 3

> Username: jwakely  
> Created_at: 2014-03-23 14:13:55 UTC  
> Url: https://github.com/boostorg/config/pull/12#issuecomment-38383588  

It could be tested now. It's available to anyone who wants it. Would it help if I report tests for Boost master vs GCC master?  (I used to do that years ago, but noone seems to any more.)

---

## Comment 4

> Username: Lastique  
> Created_at: 2014-03-23 15:03:13 UTC  
> Updated_at: 2014-03-23 15:04:46 UTC  
> Url: https://github.com/boostorg/config/pull/12#issuecomment-38384875  

More testing is always welcome. I think, this would also serve well for gcc development too. But regarding this particular issue, I'm not sure any library is currently testing std::regex as part of its tests. I discovered the problem in Boost.Log by manual tests when I tried to switch to std::regex by default. But the implementation has changed since then (partly because of this issue), and regular tests don't use std::regex anymore.  
  
There is also a general rule that we usually don't tune the code for unreleased compilers because they can change before the release. You have to be sure that the final gcc 4.9 is no worse than how it is described by Boost.Config macros. If you run the tests on 4.9 and make sure std::regex is usable, you can surely create a pull request that adjusts Boost.Config for gcc 4.9. My main request would be to ensure that Boost.Config is correct for the final 4.9, not just some pre-released version.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2014-03-25 17:14:09 UTC  
> Url: https://github.com/boostorg/config/pull/12#issuecomment-38593096  

I think it's reasonable to enable this for gcc-4.9 (which I've done in develop).  Also added slightly better tests for <regex> functionality.

---

## Comment 6

> Username: Lastique  
> Created_at: 2014-04-07 09:20:41 UTC  
> Url: https://github.com/boostorg/config/pull/12#issuecomment-39710186  

Could this be merged to master branch?

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2014-04-10 09:44:02 UTC  
> Url: https://github.com/boostorg/config/pull/12#issuecomment-40060141  

Done.

---
