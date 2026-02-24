# #969 Fix for Issue #968 - extra ; in gcc --pedantic-errors mode [Closed]

> Username: nigels-com  
> Created at: 2024-01-05 13:12:20 UTC  
> Updated at: 2024-01-08 12:09:27 UTC  
> Closed at: 2024-01-08 11:45:06 UTC  
> Url: https://github.com/boostorg/json/pull/969  

Without this fix `g++ --pedantic-errors` will fail with errors, as discussed in Issue #968  
  
Seems like a boost 1.84.0 regression, not seen with previous versions of boost.  
  
```  
nigels@zooropa:~/dev/boost-json/example$ g++ -c validate.cpp -I$(pwd)/../include -I$(pwd)/../tmp/boost_1_84_0 --pedantic-errors  
nigels@zooropa:~/dev/boost-json/example$  
```

---

## Review 1 [Commented]

> Username: grisumbras  
> Created_at: 2024-01-05 13:19:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/969#pullrequestreview-1805954964  

📁 include/boost/json/detail/parse_into.hpp

```diff
 870 |-     BOOST_JSON_HANDLE_EVENT( on_object_begin );
 871 |-     BOOST_JSON_HANDLE_EVENT( on_object_end );
 870 |+     BOOST_JSON_HANDLE_EVENT( on_object_begin )
```

> Username: grisumbras  
> Created_at: 2024-01-05 13:19:22 UTC  
> Url: https://github.com/boostorg/json/pull/969#discussion_r1442870368  

Rather than removing semicolon here and in other 100500 places, remove it in the macro definition, please.

> Username: nigels-com  
> Created_at: 2024-01-05 13:24:22 UTC  
> Url: https://github.com/boostorg/json/pull/969#discussion_r1442876947  

Ah.  The macro itself doesn't seem to be the problem.  It's ending with a function, which ought not end with a semicolon.  
  
```  
#define BOOST_JSON_HANDLE_EVENT(fn) \  
    struct do_ ## fn \  
    { \  
        template< class H, class... Args > \  
        bool operator()( H& h, Args& ... args ) const \  
        { \  
            return h. fn (args...); \  
        } \  
    }; \  
       \  
    template< class... Args > \  
    bool fn( error_code& ec, Args&& ... args ) \  
    { \  
        if( inner_active_ < 0 ) \  
        { \  
            BOOST_JSON_FAIL( ec, error::not_array ); \  
            return false; \  
        } \  
        constexpr int N = std::tuple_size<T>::value; \  
        if( inner_active_ >= N ) \  
        { \  
            BOOST_JSON_FAIL( ec, error::size_mismatch ); \  
            return false; \  
        } \  
        using F = handler_op_invoker< do_ ## fn, error_code, Args...>; \  
        using H = decltype(handlers_); \  
        return mp11::mp_with_index<N>( \  
            inner_active_, \  
            tuple_handler_op_invoker<H, F>{ \  
                handlers_, \  
                F{ std::forward_as_tuple(ec, args...) } } ); \  
    }  
  
    BOOST_JSON_HANDLE_EVENT( on_object_begin );  
    BOOST_JSON_HANDLE_EVENT( on_object_end );  
  
```

> Username: grisumbras  
> Created_at: 2024-01-08 11:40:48 UTC  
> Url: https://github.com/boostorg/json/pull/969#discussion_r1444490901  

Oh, sorry, I was confused.


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-01-05 13:43:22 UTC  
> Updated_at: 2024-01-05 13:49:42 UTC  
> Url: https://github.com/boostorg/json/pull/969#issuecomment-1878679867  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/969?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`c1e60bb`)](https://app.codecov.io/gh/boostorg/json/commit/c1e60bbfb2283563a3e356641afb6d0881a82701?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.06% compared to head [(`574b140`)](https://app.codecov.io/gh/boostorg/json/pull/969?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.06%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/969/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/969?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #969   +/-   ##  
========================================  
  Coverage    93.06%   93.06%             
========================================  
  Files           87       87             
  Lines         8107     8107             
========================================  
  Hits          7545     7545             
  Misses         562      562             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/json/pull/969?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/parse\_into.hpp](https://app.codecov.io/gh/boostorg/json/pull/969?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9wYXJzZV9pbnRvLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/969?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/969?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c1e60bb...574b140](https://app.codecov.io/gh/boostorg/json/pull/969?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-01-05 15:21:45 UTC  
> Url: https://github.com/boostorg/json/pull/969#issuecomment-1878841603  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/969/pullrequest-condensed-028a18c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/969/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/969/pullrequest.html)

---

## Comment 4

> Username: grisumbras  
> Created_at: 2024-01-08 11:45:06 UTC  
> Url: https://github.com/boostorg/json/pull/969#issuecomment-1880846728  

Merged via 942bbd4cf5e5fd30b7be71d187ab5bd2e54f8939  
Thank you for your contribution!

---

## Comment 5

> Username: nigels-com  
> Created_at: 2024-01-08 12:09:26 UTC  
> Url: https://github.com/boostorg/json/pull/969#issuecomment-1880880383  

Thanks @grisumbras .  boost::json rocks, BTW!

---
