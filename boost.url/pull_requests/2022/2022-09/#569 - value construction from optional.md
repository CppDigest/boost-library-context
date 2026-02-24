# #569 value construction from optional [Closed]

> Username: alandefreitas  
> Created at: 2022-09-26 22:01:04 UTC  
> Updated at: 2022-10-13 04:49:25 UTC  
> Closed at: 2022-10-04 01:22:25 UTC  
> Url: https://github.com/boostorg/url/pull/569  

fix #531

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-09-26 22:13:14 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1258697765  

GCOVR code coverage report [https://569.url.prtest.cppalliance.org/gcovr/index.html](https://569.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://569.url.prtest.cppalliance.org/genhtml/index.html](https://569.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2022-09-30 17:10:44 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1263817441  

In some compilers  
  
```  
iterator  
    params_ref::insert(  
        iterator before,  
        param_view const& p);  
```  
  
and  
  
```  
    BOOST_URL_DECL  
    iterator  
    params_ref::insert(  
        iterator before,  
        std::initializer_list<  
            param_view> init);  
```  
  
now became ambiguous for expressions such as  
  
```  
// params_ref qp = /* ... */;  
auto it = qp.insert(std::next(qp.begin(),0),  
                    { {"first","John"}, {"last","Doe"} });  
```  
  
To be honest, I don't know why the first overload is considered valid here by some compilers.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-09-30 17:14:07 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1263820520  

I thought I fixed this... how did you discover it?

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-09-30 17:47:51 UTC  
> Updated_at: 2022-09-30 17:48:18 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1263850229  

It just happened in CI and I was trying to fix it  
  
https://drone.cpp.al/boostorg/url/1557  
  
I think it's in this PR only

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-09-30 19:42:37 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1263953389  

```  
namespace detail {  
  
template< class T >  
struct is_optional_string;  
  
struct optional_string  
{  
    string_view s;  
    bool b = false;  
};  
  
optional_string  
get_optional_string(  
    OptionalString const& s);  
  
} // detail  
  
template< class OptionalString >  
param( string_view key, OptionalString const& value );  
```

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-09-30 20:18:41 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1263985009  

GCOVR code coverage report [https://569.url.prtest.cppalliance.org/gcovr/index.html](https://569.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://569.url.prtest.cppalliance.org/genhtml/index.html](https://569.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2022-09-30 20:28:25 UTC  
> Updated_at: 2022-10-04 00:50:03 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1263993691  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/569?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#569](https://codecov.io/gh/boostorg/url/pull/569?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5000c20) into [develop](https://codecov.io/gh/boostorg/url/commit/102ee20a05087a954e8b6e930883c792b1c93fef?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (102ee20) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 5000c20 differs from pull request most recent head 0057569. Consider uploading reports for the commit 0057569 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/569/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/569?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #569   +/-   ##  
========================================  
  Coverage    96.81%   96.81%             
========================================  
  Files          138      139    +1       
  Lines         6678     6691   +13       
========================================  
+ Hits          6465     6478   +13       
  Misses         213      213             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/569?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/pct\_string\_view.hpp](https://codecov.io/gh/boostorg/url/pull/569/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGN0X3N0cmluZ192aWV3LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/optional\_string.hpp](https://codecov.io/gh/boostorg/url/pull/569/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL29wdGlvbmFsX3N0cmluZy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/param.hpp](https://codecov.io/gh/boostorg/url/pull/569/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW0uaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/569?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/569?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [102ee20...0057569](https://codecov.io/gh/boostorg/url/pull/569?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-09-30 21:33:01 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1264043025  

GCOVR code coverage report [https://569.url.prtest.cppalliance.org/gcovr/index.html](https://569.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://569.url.prtest.cppalliance.org/genhtml/index.html](https://569.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-09-30 22:18:09 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1264083465  

GCOVR code coverage report [https://569.url.prtest.cppalliance.org/gcovr/index.html](https://569.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://569.url.prtest.cppalliance.org/genhtml/index.html](https://569.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-09-30 23:06:37 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1264118601  

GCOVR code coverage report [https://569.url.prtest.cppalliance.org/gcovr/index.html](https://569.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://569.url.prtest.cppalliance.org/genhtml/index.html](https://569.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-09-30 23:28:13 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1264127090  

GCOVR code coverage report [https://569.url.prtest.cppalliance.org/gcovr/index.html](https://569.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://569.url.prtest.cppalliance.org/genhtml/index.html](https://569.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-03 19:21:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/569#pullrequestreview-1128897096  

📁 include/boost/url/detail/optional_string.hpp

```diff
   1 |+ //
   2 |+ // Copyright (c) 2019 Vinnie Falco (vinnie.falco@gmail.com)
```

> Username: vinniefalco  
> Created_at: 2022-10-03 19:21:42 UTC  
> Updated_at: 2022-10-03 19:21:43 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986141317  

take my name off here


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-03 19:22:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/569#pullrequestreview-1128897428  

📁 include/boost/url/detail/optional_string.hpp

```diff
  12 |+ #define BOOST_URL_DETAIL_OPTIONAL_STRING_HPP
  13 |+ 
  14 |+ #include <boost/url/detail/config.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-10-03 19:22:00 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986141530  

you dont need to include config in detail files (since they are never included first)


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-03 19:22:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/569#pullrequestreview-1128898014  

📁 include/boost/url/detail/optional_string.hpp

```diff
  35 |+     optional_string>::type
  36 |+ get_optional_string(
  37 |+     StringViewable const& s)
```

> Username: vinniefalco  
> Created_at: 2022-10-03 19:22:32 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986141933  

Yucky name "string viewable"

> Username: alandefreitas  
> Created_at: 2022-10-03 20:52:21 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986219088  

StringLike?

> Username: vinniefalco  
> Created_at: 2022-10-03 22:20:35 UTC  
> Updated_at: 2022-10-03 22:20:36 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986283089  

how about just String?


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-03 19:23:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/569#pullrequestreview-1128898900  

📁 include/boost/url/detail/optional_string.hpp

```diff
  61 |+     !std::is_convertible<OptionalString, string_view>::value &&
  62 |+     std::is_convertible<typename std::decay<decltype(*std::declval<OptionalString>())>::type, string_view>::value,
  63 |+     optional_string>::type
```

> Username: vinniefalco  
> Created_at: 2022-10-03 19:23:20 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986142552  

This should be a static assert not a constraint

> Username: alandefreitas  
> Created_at: 2022-10-03 20:53:57 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986220231  

Won't this make it ambiguous with the StringLike overload?

> Username: vinniefalco  
> Created_at: 2022-10-03 22:21:27 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986283510  

umm... I'm not sure. What happens if the user tries to pass something that doesn't meet the requiremetns? i.e. is not `nullptr`, is not `none`, and is not convertible to `string_view`?


---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2022-10-03 23:58:25 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1266219384  

GCOVR code coverage report [https://569.url.prtest.cppalliance.org/gcovr/index.html](https://569.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://569.url.prtest.cppalliance.org/genhtml/index.html](https://569.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-04 00:09:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/569#pullrequestreview-1129161160  

📁 include/boost/url/detail/optional_string.hpp

```diff
  64 |+     // Please check the documentation of functions
  65 |+     // that call get_optional_string.
  66 |+     static_assert(
```

> Username: vinniefalco  
> Created_at: 2022-10-04 00:09:32 UTC  
> Updated_at: 2022-10-04 00:09:33 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986330102  

lol wow

> Username: alandefreitas  
> Created_at: 2022-10-04 00:12:21 UTC  
> Updated_at: 2022-10-04 00:12:22 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986331211  

:laughing:   
  
Is this bad?

> Username: vinniefalco  
> Created_at: 2022-10-04 00:49:01 UTC  
> Updated_at: 2022-10-04 00:49:02 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986344491  

the condition is beeg. not wrong, but its a mouthful.


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-04 00:10:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/569#pullrequestreview-1129161425  

📁 include/boost/url/detail/optional_string.hpp

```diff
  58 |+     optional_string>::type
  59 |+ get_optional_string(
  60 |+     OptionalString const& opt)
```

> Username: vinniefalco  
> Created_at: 2022-10-04 00:10:08 UTC  
> Updated_at: 2022-10-04 00:10:09 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986330350  

`noexcept`?

> Username: alandefreitas  
> Created_at: 2022-10-04 00:13:52 UTC  
> Updated_at: 2022-10-04 00:13:53 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986331791  

Is `noexcept` safe here? I can't think of a case where an implicit `operator string_view()` is not going to be exception safe, but I also can't guarantee that.


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-04 00:15:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/569#pullrequestreview-1129164093  

📁 include/boost/url/param.hpp

```diff
 351 |+         string_view key,
 352 |+         detail::optional_string const& value)
 353 |+         : param(key, value.s, value.b)
```

> Username: vinniefalco  
> Created_at: 2022-10-04 00:15:32 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986332387  

What's this overload for?

> Username: vinniefalco  
> Created_at: 2022-10-04 00:16:26 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986332707  

Ohhhhhhhhhh I see.


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-04 00:17:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/569#pullrequestreview-1129164887  

📁 include/boost/url/param.hpp

```diff
 833 |-         , value(value)
 834 |-         , has_value(true)
 778 |+         OptionalLike const& value)
```

> Username: vinniefalco  
> Created_at: 2022-10-04 00:17:14 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986332996  

I'm not sure I like seeing `OptionalLike`. What about `OptionalString`?

> Username: alandefreitas  
> Created_at: 2022-10-04 00:22:06 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986334600  

Yes. `OptionalLike` was actually a mistake. I changed all others to `OptionalString` but forgot about this one.


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-04 00:17:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/569#pullrequestreview-1129165129  

📁 test/unit/param.cpp

```diff
 127 |         }
 128 | 
 129 |+         // param(string_view, optional<string_view>)
```

> Username: vinniefalco  
> Created_at: 2022-10-04 00:17:44 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986333182  

Did you put this in the same order as the order of appearance of members in the header?

> Username: alandefreitas  
> Created_at: 2022-10-04 00:24:33 UTC  
> Updated_at: 2022-10-04 00:24:34 UTC  
> Url: https://github.com/boostorg/url/pull/569#discussion_r986335516  

Yes. 👍️


---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2022-10-04 00:33:25 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1266249536  

GCOVR code coverage report [https://569.url.prtest.cppalliance.org/gcovr/index.html](https://569.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://569.url.prtest.cppalliance.org/genhtml/index.html](https://569.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2022-10-04 00:58:20 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1266268969  

GCOVR code coverage report [https://569.url.prtest.cppalliance.org/gcovr/index.html](https://569.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://569.url.prtest.cppalliance.org/genhtml/index.html](https://569.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 24

> Username: alandefreitas  
> Created_at: 2022-10-04 01:22:25 UTC  
> Url: https://github.com/boostorg/url/pull/569#issuecomment-1266281841  

Merged

---
