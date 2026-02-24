# #140 Use uncaught_exceptions from Boost.Core to avoid C++17 warnings [Merged]

> Username: Lastique  
> Created at: 2018-12-17 15:48:39 UTC  
> Updated at: 2019-10-01 16:02:38 UTC  
> Merged at: 2018-12-18 02:08:52 UTC  
> Closed at: 2018-12-18 02:08:53 UTC  
> Url: https://github.com/boostorg/serialization/pull/140  

In C++17, `std::uncaught_exception` is deprecated in favor of `std::uncaught_exceptions`. Use portable `uncaught_exceptions` implementation from Boost.Core, which is also available for C++03.  
  
This is an alternative to https://github.com/boostorg/serialization/pull/84.

---

## Comment 1

> Username: robertramey  
> Created_at: 2019-01-06 17:45:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/140#issuecomment-451760147  

Looks like this is causing a problem somewhere outside the serialization library.  See https://www.boost.org/development/tests/develop/developer/output/Flast-FreeBSD11-boost-bin-v2-libs-serialization-test-test_array_binary_archive-test-clang-linux-6-0~gnu++11-debug-threading-multi-visibility-hidden.html

---

## Comment 2

> Username: Lastique  
> Created_at: 2019-01-06 18:31:39 UTC  
> Url: https://github.com/boostorg/serialization/pull/140#issuecomment-451763434  

I think, it should be fixed by https://github.com/boostorg/core/commit/6f3e6254e7f4980673ec0e2d3accb3252b629c0a.

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-04-13 16:31:57 UTC  
> Url: https://github.com/boostorg/serialization/pull/140#issuecomment-482833310  

Looks like it didn't make it in 1.70. Could this be merged to mater, please?

---

## Comment 4

> Username: Lastique  
> Created_at: 2019-10-01 06:21:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/140#issuecomment-536885850  

Another kind reminder to merge to master.

---

## Comment 5

> Username: robertramey  
> Created_at: 2019-10-01 14:08:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/140#issuecomment-537053631  

Hmm - I closed this because of the above quote "I think, it should be fixed by boostorg/core@6f3e625.".  So I assumed that the issue - which I never really understood myself was addressed.

---

## Comment 6

> Username: Lastique  
> Created_at: 2019-10-01 14:24:48 UTC  
> Url: https://github.com/boostorg/serialization/pull/140#issuecomment-537061218  

That comment was answering your comment https://github.com/boostorg/serialization/pull/140#issuecomment-451760147.  
  
This PR replaced uses of `std::uncaught_exception` in Boost.Serialization with the implementation from Boost.Core. This change to Boost.Serialization was merged to develop but not to master. I remind you to merge to master.

---

## Comment 7

> Username: robertramey  
> Created_at: 2019-10-01 14:33:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/140#issuecomment-537065313  

OK - normally I leave all updates in develop until I'm reminded to merge to master by the release managers.

---

## Comment 8

> Username: Lastique  
> Created_at: 2019-10-01 15:51:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/140#issuecomment-537101629  

Release managers are not responsible for tracking stale unmerged changes in develop. They will not initiate the merge unless there is a showstopper for the release.  
  
Keeping develop and master in sync is the library maintainer's responsibility.

---

## Comment 9

> Username: robertramey  
> Created_at: 2019-10-01 16:02:38 UTC  
> Url: https://github.com/boostorg/serialization/pull/140#issuecomment-537106719  

Right - no dispute about that.  But I can choose when to do it.

---
