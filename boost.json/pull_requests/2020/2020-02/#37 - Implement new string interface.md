# #37 Implement new string interface [Merged]

> Username: sdkrystian  
> Created at: 2020-02-08 21:55:39 UTC  
> Updated at: 2020-06-08 19:24:50 UTC  
> Merged at: 2020-02-11 01:10:30 UTC  
> Closed at: 2020-02-11 01:10:30 UTC  
> Url: https://github.com/boostorg/json/pull/37  

This implements the new `boost::json::string` interface, along with checked replacement and insertion. `replace` and `insert` for `InputIterator` still need to be implemented as checked.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-08 23:23:16 UTC  
> Updated_at: 2020-02-11 01:46:17 UTC  
> Url: https://github.com/boostorg/json/pull/37#issuecomment-583786851  

# [Codecov](https://codecov.io/gh/vinniefalco/json/pull/37?src=pr&el=h1) Report  
> Merging [#37](https://codecov.io/gh/vinniefalco/json/pull/37?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/json/commit/5aae31dc74d055d84a7f13e438d80cdf6005c670?src=pr&el=desc) will **decrease** coverage by `0.87%`.  
> The diff coverage is `71.83%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/json/pull/37/graphs/tree.svg?width=650&token=HNiMmIjyKi&height=150&src=pr)](https://codecov.io/gh/vinniefalco/json/pull/37?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop     #37      +/-   ##  
==========================================  
- Coverage    99.08%   98.2%   -0.88%       
==========================================  
  Files           57      57                
  Lines         4823    4856      +33       
==========================================  
- Hits          4779    4769      -10       
- Misses          44      87      +43  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/json/pull/37?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/string\_impl.hpp](https://codecov.io/gh/vinniefalco/json/pull/37/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdHJpbmdfaW1wbC5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/json/impl/string.ipp](https://codecov.io/gh/vinniefalco/json/pull/37/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc3RyaW5nLmlwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/json/string.hpp](https://codecov.io/gh/vinniefalco/json/pull/37/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/json/impl/string.hpp](https://codecov.io/gh/vinniefalco/json/pull/37/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc3RyaW5nLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/json/detail/impl/string\_impl.ipp](https://codecov.io/gh/vinniefalco/json/pull/37/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL3N0cmluZ19pbXBsLmlwcA==) | `77.83% <65.21%> (-20.11%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/json/pull/37?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/json/pull/37?src=pr&el=footer). Last update [5aae31d...7552858](https://codecov.io/gh/vinniefalco/json/pull/37?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-08 23:45:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/37#pullrequestreview-355574949  

📁 include/boost/json/detail/impl/string_impl.ipp

```diff
  14 | #include <boost/json/error.hpp>
  15 | #include <boost/json/detail/string_impl.hpp>
  16 |+ #include <boost/json/detail/impl/string_impl.hpp>
```

> Username: vinniefalco  
> Created_at: 2020-02-08 23:45:25 UTC  
> Updated_at: 2020-02-09 00:41:49 UTC  
> Url: https://github.com/boostorg/json/pull/37#discussion_r376741908  

I don't think you should be including this here, it should be included in `<boost/json/detail/string_impl.hpp>`. These directives:  
https://github.com/sdkrystian/json/blob/834b1e29f463fc391652eb22ffbaacf13540ea74/include/boost/json/detail/string_impl.hpp#L292  
  
Should read:  
```  
#include <boost/json/detail/impl/string_impl.hpp>  
#ifdef BOOST_JSON_HEADER_ONLY  
#include <boost/json/detail/impl/string_impl.ipp>  
#endif  
```

> Username: sdkrystian  
> Created_at: 2020-02-11 00:46:48 UTC  
> Updated_at: 2020-02-11 00:46:49 UTC  
> Url: https://github.com/boostorg/json/pull/37#discussion_r377400939  

Fixed; `ptr_in_range` was turned into an `inline` non-template function and moved into `detail/string_impl.hpp`


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-08 23:57:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/37#pullrequestreview-355575324  

📁 include/boost/json/impl/string.hpp

```diff
  88 |+ 
  89 |+ // KRYSTIAN TODO: this can be done without copies when
  90 |+ // reallocation is not needed.
```

> Username: vinniefalco  
> Created_at: 2020-02-08 23:57:47 UTC  
> Updated_at: 2020-02-09 00:41:49 UTC  
> Url: https://github.com/boostorg/json/pull/37#discussion_r376742422  

Fix the comment to explain that we should use a separate algorithm if iterator category is Forward or better

> Username: sdkrystian  
> Created_at: 2020-02-11 00:45:50 UTC  
> Updated_at: 2020-02-11 00:45:51 UTC  
> Url: https://github.com/boostorg/json/pull/37#discussion_r377400678  

Done


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-08 23:58:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/37#pullrequestreview-355575330  

📁 include/boost/json/impl/string.hpp

```diff
 118 |+     cleanup c{tmp, dsp};
 119 |+     traits_type::copy(
 120 |+         impl_.replace_unchecked(first - begin(), last - first, tmp.size(), sp_),
```

> Username: vinniefalco  
> Created_at: 2020-02-08 23:58:05 UTC  
> Updated_at: 2020-02-09 00:41:49 UTC  
> Url: https://github.com/boostorg/json/pull/37#discussion_r376742433  

long line.. out of balance with the other lines and blocks the chi. consider:  
```  
traits_type::copy(  
     impl_.replace_unchecked(  
         first - begin(),  
         last - first,  
         tmp.size(),  
         sp_),  
     tmp.data(),  
     tmp.size());  
return *this;  
```

> Username: sdkrystian  
> Created_at: 2020-02-11 00:45:40 UTC  
> Url: https://github.com/boostorg/json/pull/37#discussion_r377400638  

Moved to multiple lines.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-09 00:02:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/37#pullrequestreview-355575464  

📁 test/string.cpp

```diff
1580 |-                     t.v1).insert(1, t.s2, 1, 3));
1581 |-             });
1573 |+         //// insert(size_type, string const&, size_type, size_type)
```

> Username: vinniefalco  
> Created_at: 2020-02-09 00:02:17 UTC  
> Updated_at: 2020-02-09 00:41:49 UTC  
> Url: https://github.com/boostorg/json/pull/37#discussion_r376742602  

need a comment explaining why this is disabled, put your name in caps `// SDK` or something like that so the reader knows it is editorial.

> Username: sdkrystian  
> Created_at: 2020-02-11 00:45:28 UTC  
> Updated_at: 2020-02-11 00:45:29 UTC  
> Url: https://github.com/boostorg/json/pull/37#discussion_r377400584  

Added


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-09 00:02:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/37#pullrequestreview-355575473  

📁 test/string.cpp

```diff
2257 |+                 std::string s1(t.v2.data(), t.v2.size());
2258 |+                 string s2(t.v2, sp);
2259 |+                 BOOST_TEST(s2.replace(s2.begin(), s2.begin() + 1, t.v2.substr(0)) == s1.replace(0, 1, t.v2.data(), t.v2.size()));
```

> Username: vinniefalco  
> Created_at: 2020-02-09 00:02:37 UTC  
> Updated_at: 2020-02-09 00:41:49 UTC  
> Url: https://github.com/boostorg/json/pull/37#discussion_r376742611  

This is hard to read

> Username: vinniefalco  
> Created_at: 2020-02-09 00:04:45 UTC  
> Updated_at: 2020-02-09 00:41:49 UTC  
> Url: https://github.com/boostorg/json/pull/37#discussion_r376742710  

Reformatted:  
```  
BOOST_TEST(  
    s2.replace(  
        s2.begin(),  
        s2.begin() + 1,  
        t.v2.substr(0)) ==  
    s1.replace(  
        0, 1,  
        t.v2.data(),  
        t.v2.size()));  
```  
Now that I can read it... we are using the iterator overload of `json::string` but the index overload of `std::string`? Why?

> Username: sdkrystian  
> Created_at: 2020-02-11 00:45:17 UTC  
> Url: https://github.com/boostorg/json/pull/37#discussion_r377400527  

Reformatted in new commit.  
  
> Now that I can read it... we are using the iterator overload of `json::string` but the index overload of `std::string`? Why?  
  
They perform the same operation, it was just easier to copy the index based tests.


---

## Comment 7

> Username: vinniefalco  
> Created_at: 2020-02-09 00:08:25 UTC  
> Url: https://github.com/boostorg/json/pull/37#issuecomment-583789804  

Looks great, minor style nits, but the tests need some work.

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2020-02-09 00:14:57 UTC  
> Url: https://github.com/boostorg/json/pull/37#issuecomment-583790339  

45 uncovered lines here:  
https://codecov.io/gh/vinniefalco/json/src/834b1e29f463fc391652eb22ffbaacf13540ea74/include/boost/json/detail/impl/string_impl.ipp

---
