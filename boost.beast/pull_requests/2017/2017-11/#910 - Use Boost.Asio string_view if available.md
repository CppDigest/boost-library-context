# #910 Use Boost.Asio string_view if available. [Closed]

> Username: Jiwan  
> Created at: 2017-11-26 21:47:17 UTC  
> Updated at: 2022-06-22 20:05:00 UTC  
> Closed at: 2022-06-22 20:05:00 UTC  
> Url: https://github.com/boostorg/beast/pull/910  

This PR tries to align **Boost.Beast**'s string_view with **Boost.Asio**. Since ``std::string_view`` and ``std::experimental::string_view`` do not have a ``clear`` member function, ``a_string_view = string_view();`` is used instead ([see n4288](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4288.html)).

---

## Comment 1

> Username: Jiwan  
> Created_at: 2017-11-26 21:49:05 UTC  
> Url: https://github.com/boostorg/beast/pull/910#issuecomment-347041156  

Note that I am still unsure if using the ``detail`` folder of **Boost.Asio** is wise.

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2017-11-26 22:19:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/910#pullrequestreview-79039912  

📁 include/boost/beast/core/string.hpp

```diff
  11 | #define BOOST_BEAST_STRING_HPP
  12 | 
  13 |+ #include <boost/asio/detail/string_view.hpp>
```

> Username: vinniefalco  
> Created_at: 2017-11-26 22:19:09 UTC  
> Updated_at: 2017-11-27 10:22:19 UTC  
> Url: https://github.com/boostorg/beast/pull/910#discussion_r153083837  

Hmm... I don't know about this! Including a detail header is not best practices


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2017-11-26 22:19:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/910#pullrequestreview-79039922  

📁 include/boost/beast/core/string.hpp

```diff
  17 |+ #  define BOOST_ASIO_HAS_STRING_VIEW 0
  18 |+ # else
  19 |+ #  define BOOST_ASIO_HAS_STRING_VIEW 1
```

> Username: vinniefalco  
> Created_at: 2017-11-26 22:19:26 UTC  
> Updated_at: 2017-11-27 10:22:19 UTC  
> Url: https://github.com/boostorg/beast/pull/910#discussion_r153083849  

I don't think we are allowed to adjust these macros. The user needs to adjust them not our library.

> Username: Jiwan  
> Created_at: 2017-11-27 10:23:09 UTC  
> Updated_at: 2017-11-27 10:23:14 UTC  
> Url: https://github.com/boostorg/beast/pull/910#discussion_r153155767  

I simplified things a bit more.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2017-11-26 22:20:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/910#pullrequestreview-79039963  

📁 include/boost/beast/core/string.hpp

```diff
  26 |+ #if BOOST_ASIO_NO_STRING_VIEW
  27 | #include <boost/utility/string_view.hpp>
  28 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2017-11-26 22:20:48 UTC  
> Updated_at: 2017-11-27 10:22:19 UTC  
> Url: https://github.com/boostorg/beast/pull/910#discussion_r153083881  

I think we need @chriskohlhoff to weigh in on this, to decide if he wants to support other libraries piggy-backing on the string view declaration.


---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2017-11-27 10:36:10 UTC  
> Updated_at: 2017-11-27 11:03:17 UTC  
> Url: https://github.com/boostorg/beast/pull/910#issuecomment-347142506  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/910?src=pr&el=h1) Report  
> Merging [#910](https://codecov.io/gh/boostorg/beast/pull/910?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/5d83c79be408c8a29dd7eec413e9dc699648cdba?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `90%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/910/graphs/tree.svg?src=pr&token=Gq6MFA9JRF&width=650&height=150)](https://codecov.io/gh/boostorg/beast/pull/910?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #910   +/-   ##  
========================================  
  Coverage    95.68%   95.68%             
========================================  
  Files          104      104             
  Lines        10441    10441             
========================================  
  Hits          9990     9990             
  Misses         451      451  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/910?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/string.hpp](https://codecov.io/gh/boostorg/beast/pull/910?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0cmluZy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/detail/rfc7230.hpp](https://codecov.io/gh/boostorg/beast/pull/910?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2RldGFpbC9yZmM3MjMwLmhwcA==) | `85.08% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/fields.ipp](https://codecov.io/gh/boostorg/beast/pull/910?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `97.72% <87.5%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/910?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/910?src=pr&el=footer). Last update [5d83c79...dfac811](https://codecov.io/gh/boostorg/beast/pull/910?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2018-01-22 19:21:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/910#pullrequestreview-90593931  

📁 include/boost/beast/http/detail/rfc7230.hpp

```diff
 356 |-     v.second.clear();
 355 |+     v.first = string_view();
 356 |+     v.second = string_view();
```

> Username: vinniefalco  
> Created_at: 2018-01-22 19:21:22 UTC  
> Url: https://github.com/boostorg/beast/pull/910#discussion_r163040894  

Consider this instead:  
```  
v.first = {};  
v.second = {};  
```

> Username: vinniefalco  
> Created_at: 2018-01-22 19:23:04 UTC  
> Url: https://github.com/boostorg/beast/pull/910#discussion_r163041401  

Actually, I'll make this change myself in its own commit.

> Username: Jiwan  
> Created_at: 2018-01-22 20:31:24 UTC  
> Url: https://github.com/boostorg/beast/pull/910#discussion_r163059699  

:+1:


---

## Comment 7

> Username: vinniefalco  
> Created_at: 2018-02-15 03:21:56 UTC  
> Url: https://github.com/boostorg/beast/pull/910#issuecomment-365815434  

Does this still use detail items from Asio or does it only use Asio's public interfaces?

---

## Comment 8

> Username: stale[bot]  
> Created_at: 2018-04-24 16:52:18 UTC  
> Url: https://github.com/boostorg/beast/pull/910#issuecomment-384003464  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: stale[bot]  
> Created_at: 2018-05-01 17:32:27 UTC  
> Url: https://github.com/boostorg/beast/pull/910#issuecomment-385733368  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 10

> Username: omartijn  
> Created_at: 2020-02-09 18:33:53 UTC  
> Url: https://github.com/boostorg/beast/pull/910#issuecomment-583877673  

It'd be really great if this issue could be revisited. The fact that asio and beast use different string_view implementations feels sub-optimal. It's mostly small things, like not being able to directly use `boost::beast::http::message::operator[](boost::beast::http::field::host)` as parameter to `boost::asio::ip::tcp::resolver::async_resolve`.  
  
It's trivial to work around, but it doesn't make the code more readable. In addition, I quite like the idea of using `std::string_view` (or `std::experimental::string_view`) when available.

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2020-02-09 20:32:52 UTC  
> Url: https://github.com/boostorg/beast/pull/910#issuecomment-583889880  

Yes I believe we can do something. In "stand-alone" (not implemented yet) mode Beast can use standalone Asio, require C++17, and use `std::string_view`.  
  
Otherwise, Beast can require C++11 and use Boost.Asio. In this configuration, Beast will use `boost::string_view`, unless the Asio macro `BOOST_ASIO_HAS_STD_STRING_VIEW` is defined, in which case Beast will use `std::string_view` and require C++17.

---

## Comment 12

> Username: stale[bot]  
> Created_at: 2020-03-11 02:26:04 UTC  
> Url: https://github.com/boostorg/beast/pull/910#issuecomment-597411326  

This issue has been open for a while with no activity, has it been resolved?

---
