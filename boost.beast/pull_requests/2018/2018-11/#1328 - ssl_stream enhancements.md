# #1328 ssl_stream enhancements [Closed]

> Username: octobanana  
> Created at: 2018-11-27 22:15:33 UTC  
> Updated at: 2018-12-02 22:56:33 UTC  
> Closed at: 2018-12-01 15:02:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1328  

* Use std::make_unique instead of new in construction of std::unique_ptr.  
* Explicitly make the object non-copyable by deleting the copy constructor and copy assignment operator.  
* Remove the non-owning pointer to the ssl::context private member variable, due to it being unused after constructing the underlying stream.  
* Fix a few style inconsistencies.  
* Use the default move constructor and default move assignment operator.

---

## Comment 1

> Username: octobanana  
> Created_at: 2018-11-27 22:17:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#issuecomment-442238957  

Thoughts?

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-11-27 22:35:27 UTC  
> Updated_at: 2018-11-28 01:13:25 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#issuecomment-442244222  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1328?src=pr&el=h1) Report  
> Merging [#1328](https://codecov.io/gh/boostorg/beast/pull/1328?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/770308cdcc9b758f7e282e886c73d7efde529e57?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1328/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1328?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1328   +/-   ##  
========================================  
  Coverage    92.05%   92.05%             
========================================  
  Files          124      124             
  Lines        11505    11505             
========================================  
  Hits         10591    10591             
  Misses         914      914  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1328?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1328?src=pr&el=footer). Last update [770308c...c79e8ee](https://codecov.io/gh/boostorg/beast/pull/1328?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2018-11-27 22:36:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1328#pullrequestreview-179044801  

📁 include/boost/beast/_experimental/core/ssl_stream.hpp

```diff
 107 |-         : p_(new stream_type{
 108 |-             std::forward<Arg>(arg), ctx})
 107 |+         : p_(std::make_unique<stream_type>(
```

> Username: vinniefalco  
> Created_at: 2018-11-27 22:36:37 UTC  
> Updated_at: 2018-11-28 00:52:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#discussion_r236875150  

This is available in C++14 and later, while Beast only requires C++11

> Username: octobanana  
> Created_at: 2018-11-27 22:51:41 UTC  
> Updated_at: 2018-11-28 00:52:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#discussion_r236879453  

Oops, correct, I forgot it was a 14 and up feature.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2018-11-27 22:39:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1328#pullrequestreview-179045885  

📁 include/boost/beast/_experimental/core/ssl_stream.hpp

```diff
 133 | 
 134 |+     /// Default Deconstructor
 135 |+     ~ssl_stream() = default;
```

> Username: vinniefalco  
> Created_at: 2018-11-27 22:39:52 UTC  
> Updated_at: 2018-11-28 00:52:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#discussion_r236876071  

hmm...I'm not sure that this actually changes anything, the copy members were already implicitly disabled because they are not available in the underlying stream. And the destructor was already implicitly defaulted. What was the intent here?

> Username: octobanana  
> Created_at: 2018-11-27 22:53:24 UTC  
> Updated_at: 2018-11-28 00:52:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#discussion_r236879934  

To be more explicit, but you're right, it's redundant since they are not available in the underlying stream.

> Username: vinniefalco  
> Created_at: 2018-11-27 22:54:27 UTC  
> Updated_at: 2018-11-28 00:52:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#discussion_r236880224  

The less code the better, especially because defaulted special members can sometimes confuse the coverage reports.

> Username: octobanana  
> Created_at: 2018-11-27 22:57:51 UTC  
> Updated_at: 2018-11-28 00:52:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#discussion_r236881097  

So do you prefer having the move ctor and move assignment to not use the '= default'?

> Username: vinniefalco  
> Created_at: 2018-11-28 00:02:12 UTC  
> Updated_at: 2018-11-28 00:52:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#discussion_r236895618  

I think it is better to just leave them unspecified and let the compiler implicitly delete them (or generate them, whatever the case may be). The rules for what the compiler does in each case are in this presentation from @HowardHinnant https://stackoverflow.com/questions/24342941/what-are-the-rules-for-automatic-generation-of-move-operations

> Username: octobanana  
> Created_at: 2018-11-28 00:19:10 UTC  
> Updated_at: 2018-11-28 00:52:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#discussion_r236898862  

That diagram is helpful, thanks.

> Username: vinniefalco  
> Created_at: 2018-11-28 00:35:39 UTC  
> Updated_at: 2018-11-28 00:52:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#discussion_r236901961  

I tend to forget so don't be surprised if you find several places in beast where I have unnecessary explicitly defaulted special members.


---

## Comment 5

> Username: vinniefalco  
> Created_at: 2018-11-27 22:43:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#issuecomment-442246549  

It looks like the only meaningful changes are the removal of the unused pointer (great!) and fixing the whitespace...

---

## Review 6 [Commented]

> Username: octobanana  
> Created_at: 2018-11-28 00:32:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1328#pullrequestreview-179076402  

📁 include/boost/beast/_experimental/core/ssl_stream.hpp

```diff
 110 | 
 111 |     /// Move Constructor
 114 |-     ssl_stream(ssl_stream&& other)
```

> Username: octobanana  
> Created_at: 2018-11-28 00:32:43 UTC  
> Updated_at: 2018-11-28 00:52:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#discussion_r236901442  

Since the move constructor and move assignment are implicitly generated, this could be removed.

> Username: djarek  
> Created_at: 2018-11-28 12:32:20 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#discussion_r237064157  

Note that this changes the behavior: `.get_executor()` on a moved-from stream will now be UB (null-deref). Perhaps the behavior on move of this stream should be documented? (it differs from how I/O objects in ASIO behave).

> Username: djarek  
> Created_at: 2018-11-28 12:35:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1328#discussion_r237064983  

Ehh, I just noticed `.get_executor()` already behaves like that, so no change. Sorry for the noise!


---
