# #575 JSON pointer implementation [Merged]

> Username: grisumbras  
> Created at: 2021-05-23 15:13:24 UTC  
> Updated at: 2022-03-02 21:28:15 UTC  
> Merged at: 2022-03-02 21:27:54 UTC  
> Closed at: 2022-03-02 21:27:54 UTC  
> Url: https://github.com/boostorg/json/pull/575  

* Class `pointer` in `boost/json/pointer.hpp`; stores a `string_view` that should contain a JSON Pointer string.  
* Member function `value& value::at(pointer)` that either returns a reference to corresponding child or throws `system_error`.  
* Member function `value* value::find(pointer, error_code&)` that either returns a pointer to corresponding child or sets `error_code` to some error.  
* `const` versions of those functions.  
* Extend `error` and `error_condition` enums with values needed for Pointer.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-05-23 15:25:03 UTC  
> Updated_at: 2022-03-02 10:52:41 UTC  
> Url: https://github.com/boostorg/json/pull/575#issuecomment-846580435  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/575?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#575](https://codecov.io/gh/boostorg/json/pull/575?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d6f9919) into [develop](https://codecov.io/gh/boostorg/json/commit/59cccb17e41921b079ee143e2ed6ddc19f757814?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (59cccb1) will **increase** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head d6f9919 differs from pull request most recent head c901d28. Consider uploading reports for the commit c901d28 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/575/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/575?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #575      +/-   ##  
===========================================  
+ Coverage    99.05%   99.07%   +0.01%       
===========================================  
  Files           68       70       +2       
  Lines         6375     6290      -85       
===========================================  
- Hits          6315     6232      -83       
+ Misses          60       58       -2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/575?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/object.hpp](https://codecov.io/gh/boostorg/json/pull/575/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9vYmplY3QuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/object.hpp](https://codecov.io/gh/boostorg/json/pull/575/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/boostorg/json/pull/575/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.98% <ø> (+0.08%)` | :arrow_up: |  
| [include/boost/json/detail/digest.hpp](https://codecov.io/gh/boostorg/json/pull/575/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9kaWdlc3QuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/error.ipp](https://codecov.io/gh/boostorg/json/pull/575/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvZXJyb3IuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.hpp](https://codecov.io/gh/boostorg/json/pull/575/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.ipp](https://codecov.io/gh/boostorg/json/pull/575/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/pointer.ipp](https://codecov.io/gh/boostorg/json/pull/575/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcG9pbnRlci5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/pointer.hpp](https://codecov.io/gh/boostorg/json/pull/575/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3BvaW50ZXIuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/string.hpp](https://codecov.io/gh/boostorg/json/pull/575/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| ... and [39 more](https://codecov.io/gh/boostorg/json/pull/575/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/575?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/575?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [59cccb1...c901d28](https://codecov.io/gh/boostorg/json/pull/575?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-05-23 16:09:11 UTC  
> Url: https://github.com/boostorg/json/pull/575#issuecomment-846586984  

Maybe we should remove all the trailing whitespace in the entire repo _once_ instead of doing it in every pull request?

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-23 16:09:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-666283417  

📁 include/boost/json/error.hpp

```diff
 235 |+ 
 236 |+     /// current value does not contain referenced value
 237 |+     not_found,
```

> Username: vinniefalco  
> Created_at: 2021-05-23 16:09:35 UTC  
> Updated_at: 2021-05-23 16:09:39 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r637568058  

trailing comma on the last element, is this valid C++?

> Username: grisumbras  
> Created_at: 2021-05-23 17:22:22 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r637576727  

Yes https://eel.is/c++draft/dcl.enum#nt:enum-specifier

> Username: vinniefalco  
> Created_at: 2021-05-23 17:27:13 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r637577163  

Make sure Doxygen doesn't choke on it


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2021-05-23 16:29:00 UTC  
> Url: https://github.com/boostorg/json/pull/575#issuecomment-846589702  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest-condensed-a369f0b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html)

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-23 17:05:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-666287773  

📁 include/boost/json/impl/error.ipp

```diff
 129 |                 return "An error occurred during assignment";
 130 |+             case condition::pointer_parse_error:
 131 |+                 return "A JSON pointer parse error occurred";
```

> Username: vinniefalco  
> Created_at: 2021-05-23 17:05:32 UTC  
> Updated_at: 2021-05-23 17:05:33 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r637574877  

"JSON Pointer" note capitalization


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-23 17:06:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-666287861  

📁 include/boost/json/impl/pointer.ipp

```diff
  45 |+         }
  46 |+ 
  47 |+         result = result * 10 + c - '0';
```

> Username: vinniefalco  
> Created_at: 2021-05-23 17:06:41 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r637574990  

You have to check for overflow here


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-23 17:07:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-666287919  

📁 include/boost/json/impl/pointer.ipp

```diff
 124 |+         if ( auto object = result->if_object() )
 125 |+         {
 126 |+             char buffer[256];
```

> Username: vinniefalco  
> Created_at: 2021-05-23 17:07:43 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r637575114  

Hard coded buffer size? Is overflow possible?

> Username: grisumbras  
> Created_at: 2021-05-23 17:23:42 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r637576848  

I'm going to control buffer size with a macro.

> Username: vinniefalco  
> Created_at: 2021-05-23 17:27:35 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r637577190  

Can't we do better than that?

> Username: grisumbras  
> Created_at: 2021-05-23 17:41:19 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r637578641  

Using a local buffer allows to make this function `noexcept`.  Also, if we allocate dynamically, we'd have to provide some way for allocation customization and that would clutter the API.

> Username: vinniefalco  
> Created_at: 2021-05-23 18:01:36 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r637580891  

>  if we allocate dynamically, we'd have to provide some way for allocation customization and that would clutter the API  
  
I don't necessarily agree. We could have a local buffer, and go to the heap if the buffer is insufficient. We don't have to allow customization of the allocation if the memory is allocated and destroyed in the same function (i.e. if it is temporary). It becomes an implementation detail. I have done this in other places, such as the `stream_parser` on a suspend.


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-23 17:09:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-666288030  

📁 include/boost/json/pointer.hpp

```diff
  16 |+ BOOST_JSON_NS_BEGIN
  17 |+ 
  18 |+ /** Path to JSON value nested element
```

> Username: vinniefalco  
> Created_at: 2021-05-23 17:09:17 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r637575277  

These javadocs need to be complete

> Username: grisumbras  
> Created_at: 2021-05-23 17:23:55 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r637576881  

Sure, this is just a draft.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-23 17:09:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-666288036  

📁 include/boost/json/pointer.hpp

```diff
  18 |+ /** Path to JSON value nested element
  19 |+  */
  20 |+ class pointer {
```

> Username: vinniefalco  
> Created_at: 2021-05-23 17:09:26 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r637575283  

Can we please put opening braces on their own line


---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2021-05-23 20:08:59 UTC  
> Url: https://github.com/boostorg/json/pull/575#issuecomment-846617760  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest-condensed-0d7f35e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2021-05-25 16:48:58 UTC  
> Url: https://github.com/boostorg/json/pull/575#issuecomment-848043536  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest-condensed-babc7e9.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html)

---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 14:44:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669117082  

📁 include/boost/json/impl/pointer.ipp

```diff
 120 |- parse_done:
 121 |-     return string_view(start, out - start);
 206 |+     return {start, b};;
```

> Username: vinniefalco  
> Created_at: 2021-05-26 14:44:36 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639797745  

double semicolon


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 16:17:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669236828  

📁 include/boost/json/impl/error.ipp

```diff
  54 |+             case error::invalid_escape: return "invalid escape sequence";
  55 |+             case error::token_not_number: return "token is not a number";
  56 |+             case error::value_is_scalar: return "current value is scalar";
```

> Username: vinniefalco  
> Created_at: 2021-05-26 16:17:17 UTC  
> Updated_at: 2021-05-26 16:17:18 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639886941  

Why are these indented differently from the others

> Username: grisumbras  
> Created_at: 2021-05-26 16:34:54 UTC  
> Updated_at: 2021-05-26 16:34:55 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639907884  

I actually thought that the old indent is a mistake. But if it's deliberate, I'll move new lines to the left.

> Username: vinniefalco  
> Created_at: 2021-05-26 16:50:37 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639921348  

Not a mistake. I try to keep code narrow so it can fit in thinner places like smartphones or in an IDE window with 3 file panes. Since this code is declarative rather than imperative (i.e. it simply provides data in the form of strings and not an algorithm in the form of code), I prioritize shrinking the width measured from the first column over scope-correct indentation.


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 16:48:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669283951  

📁 include/boost/json/detail/object.hpp

```diff
  87 |+ find_in_object<string_view>(
  88 |+     object const&,
  89 |+     string_view key) noexcept;
```

> Username: vinniefalco  
> Created_at: 2021-05-26 16:48:32 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639919826  

The "extern template" thing is a tiny bit risky, in the sense that there is a higher chance it will fail on some compiler.  There is also the question of linkage and export, for shared libraries. It would be better if this could be expressed without explicit function template instantiation, even if it means forward declaring a type specific to JSON Pointer.


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 16:48:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669285081  

📁 include/boost/json/error.hpp

```diff
 216 |+ 
 217 |+     //
 218 |+     // pointer errors
```

> Username: vinniefalco  
> Created_at: 2021-05-26 16:48:54 UTC  
> Updated_at: 2021-05-26 16:48:55 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639920141  

"JSON Pointer errors"


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 16:52:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669296191  

📁 include/boost/json/impl/object.ipp

```diff
  69 |+ find_in_object<string_view>(
  70 |+     object const& obj,
  71 |+     string_view key) noexcept;
```

> Username: vinniefalco  
> Created_at: 2021-05-26 16:52:38 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639922831  

What is this

> Username: grisumbras  
> Created_at: 2021-05-27 05:38:13 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r640301066  

Explicit template instantiation. This puts the template instantiation into the compiled library.


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 16:58:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669312375  

📁 include/boost/json/impl/pointer.ipp

```diff
  24 |+ 
  25 |+     pointer_token(
  26 |+         string_view::const_iterator b,
```

> Username: vinniefalco  
> Created_at: 2021-05-26 16:58:03 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639926989  

kind of a long winded way to write `char const*` no?

> Username: grisumbras  
> Created_at: 2021-05-27 05:43:08 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r640303049  

Apparently `string_view::const_iterator` is not `char const*` on msvs 14. I guess, I could try changing it from the other side and use `string_view::const_iterator` less.

> Username: vinniefalco  
> Created_at: 2021-05-27 12:48:05 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r640589467  

What is the type on msvs 14.1?


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 16:58:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669312554  

📁 include/boost/json/impl/pointer.ipp

```diff
  26 |+         string_view::const_iterator b,
  27 |+         string_view::const_iterator e) noexcept
  28 |+         : b_(b), e_(e)
```

> Username: vinniefalco  
> Created_at: 2021-05-26 16:58:14 UTC  
> Updated_at: 2021-05-26 16:58:15 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639927232  

separate lines please


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 16:59:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669313299  

📁 include/boost/json/impl/pointer.ipp

```diff
  59 |+             {
  60 |+                 BOOST_ASSERT('1' == c);
  61 |+                 return '/';
```

> Username: vinniefalco  
> Created_at: 2021-05-26 16:59:05 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639928218  

Style: I would write this as  
```  
if ( '0' == c )  
    return '~';  
BOOST_ASSERT('1' == c);  
return '/';  
```


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:00:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669314328  

📁 include/boost/json/impl/pointer.ipp

```diff
  75 |+         {
  76 |+             ++base_;
  77 |+         }
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:00:13 UTC  
> Updated_at: 2021-05-26 17:01:40 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639929580  

I prefer not to use the braces for single statements, e.g.  
```  
        if ( '~' == *base_ )  
            base_ += 2;  
        else  
            ++base_;  
```


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:08:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669321531  

📁 include/boost/json/impl/pointer.ipp

```diff
 120 |+             return false;
 121 |+         }
 122 |+     }
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:08:01 UTC  
> Updated_at: 2021-05-26 17:08:02 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639939107  

Too many statements on the same line, which confuses coverage and makes setting breakpoints more difficult. I would rewrite it to use fewer compound expressions:  
```  
bool  
operator==(  
    pointer_token token,  
    string_view sv) noexcept  
{  
    auto n = sv.size();  
    auto s = sv.data();  
    auto p = token.begin();  
    auto const end = token.end();  
    while(n--)  
    {  
        if(p == end)  
            return false;  
        if(*p != *s)  
            return false;  
        ++p;  
        ++s;  
    }  
    if(p != end)  
        return false;  
    return true;  
}  
```


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:09:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669322573  

📁 include/boost/json/impl/pointer.ipp

```diff
 131 |+     error_code& ec) noexcept
 132 |+ {
 133 |+     if ( ( b == e ) || ( *b == '0' && ( b + 1 ) != e  && b[1] != '/' ) )
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:09:10 UTC  
> Updated_at: 2021-05-26 17:09:11 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639940554  

LOL... coverage! Put each condition on its own line...  
```  
if (  
    ( b == e ) ||  
    ( *b == '0' && ( b + 1 )  != e && b[1] != '/' ) )  
```


---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:09:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669322722  

📁 include/boost/json/impl/pointer.ipp

```diff
 145 |+         {
 146 |+             break;
 147 |+         }
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:09:19 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639940741  

No braces needed


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:10:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669323408  

📁 include/boost/json/impl/pointer.ipp

```diff
 147 |+         }
 148 |+ 
 149 |+         if ( c < '0' || c > '9' )
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:10:06 UTC  
> Updated_at: 2021-05-26 17:10:34 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639941684  

Would `if( (static_cast<unsigned char>(c) - '0') > 9 )` be better?

> Username: grisumbras  
> Created_at: 2021-05-27 10:23:00 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r640493888  

Not like that. It would be equivalent to `((int)c - (int)'0') > 9`. But I see how to make something similar work.


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:11:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669324760  

📁 include/boost/json/impl/pointer.ipp

```diff
 164 |+             {
 165 |+                 result += d;
 166 |+                 continue;
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:11:25 UTC  
> Updated_at: 2021-05-26 17:11:26 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639943425  

There's an easier way to check for unsigned overflow... just test if the new value is _smaller_ than the old value (i.e. it wrapped around).


---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:12:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669326295  

📁 include/boost/json/impl/pointer.ipp

```diff
 220 |+     {
 221 |+         return nullptr;
 222 |+     }
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:12:01 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639944149  

braces


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:12:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669326456  

📁 include/boost/json/impl/pointer.ipp

```diff
 226 |+     {
 227 |+         return nullptr;
 228 |+     }
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:12:05 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639944238  

braces


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:12:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669327256  

📁 include/boost/json/impl/pointer.ipp

```diff
 247 |+         }
 248 |+ 
 249 |+         if ( auto object = result->if_object() )
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:12:51 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639945659  

sus variable name... e.g. use `po` instead.


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:13:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669327606  

📁 include/boost/json/impl/pointer.ipp

```diff
 258 |+             cur = token.end().base();
 259 |+         }
 260 |+         else if ( auto array = result->if_array() )
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:13:14 UTC  
> Updated_at: 2021-05-26 17:13:15 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639946321  

sus variable name ... "`pa`" maybe?


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:13:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669327908  

📁 include/boost/json/impl/pointer.ipp

```diff
 260 |+         else if ( auto array = result->if_array() )
 261 |+         {
 262 |+             std::size_t index = detail::parse_number_token(cur, ptr_str.end(),
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:13:34 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639946707  

I would use `auto` here


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:13:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669328013  

📁 include/boost/json/impl/pointer.ipp

```diff
 265 |+             {
 266 |+                 return nullptr;
 267 |+             }
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:13:40 UTC  
> Updated_at: 2021-05-26 17:13:41 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639946836  

braces


---

## Review 32 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:14:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669328669  

📁 include/boost/json/impl/pointer.ipp

```diff
 267 |+             }
 268 |+ 
 269 |+             result = array->if_contains(index);
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:14:04 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639947293  

`array` names a type... red flags should be going off in your head when you see this line


---

## Review 33 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:14:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669329432  

📁 include/boost/json/impl/pointer.ipp

```diff
 295 |+     {
 296 |+         detail::throw_system_error(ec, BOOST_JSON_SOURCE_POS);
 297 |+     }
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:14:27 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639947843  

braces


---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:15:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669330043  

📁 include/boost/json/impl/pointer.ipp

```diff
 302 |+ 
 303 |+ value const&
 304 |+ value::at(pointer ptr) const
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:15:12 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639948913  

You put member function definitions for `value` in pointer.ipp?

> Username: grisumbras  
> Created_at: 2021-05-27 11:01:34 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r640518394  

I thought it would be reasonable to put all code related to Pointer into a separate file. But if you think it belongs in `value.ipp`, then I'll move it.


---

## Review 35 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:16:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669330782  

📁 include/boost/json/pointer.hpp

```diff
  27 |+     pointer() noexcept
  28 |+         : pointer("")
  29 |+     {}
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:16:01 UTC  
> Updated_at: 2021-05-26 17:16:02 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639949928  

braces on separate lines for breakpoints and coverage


---

## Review 36 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:16:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669331095  

📁 include/boost/json/pointer.hpp

```diff
  36 |+ 
  37 |+     string_view
  38 |+     string() const noexcept
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:16:21 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639950351  

sus member function name, it names the type `json::string`

> Username: grisumbras  
> Created_at: 2021-05-27 11:04:30 UTC  
> Updated_at: 2021-05-27 11:04:31 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r640520259  

Any suggestions for a better name?


---

## Review 37 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-26 17:16:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-669331695  

📁 test/pointer.cpp

```diff
  11 |+ #include <boost/json/pointer.hpp>
  12 |+ // Test header guards
  13 |+ #include <boost/json/pointer.hpp>
```

> Username: vinniefalco  
> Created_at: 2021-05-26 17:16:59 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r639951162  

lol...


---

## Comment 38

> Username: cppalliance-bot  
> Created_at: 2021-05-26 17:34:06 UTC  
> Url: https://github.com/boostorg/json/pull/575#issuecomment-848975186  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest-condensed-4ee6658.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html)

---

## Comment 39

> Username: cppalliance-bot  
> Created_at: 2021-05-27 14:09:04 UTC  
> Url: https://github.com/boostorg/json/pull/575#issuecomment-849666500  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest-condensed-d910868.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html)

---

## Review 40 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-27 14:53:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-670290435  

📁 include/boost/json/impl/pointer.ipp

```diff
  28 |-         : b_(b), e_(e)
  27 |+         : b_(b)
  28 |+         , e_(e)
```

> Username: vinniefalco  
> Created_at: 2021-05-27 14:53:48 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r640702797  

Note that this isn't just style... putting each statement on its own line makes it easier to get a breakpoint, and see coverage.


---

## Comment 41

> Username: cppalliance-bot  
> Created_at: 2021-10-01 07:54:17 UTC  
> Url: https://github.com/boostorg/json/pull/575#issuecomment-931999248  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest-condensed-1b9fd60.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html)

---

## Review 42 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-05 16:04:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-844778832  

📁 include/boost/json/impl/object.ipp

```diff
  26 |+ namespace detail {
  27 |+ 
  28 |+ template<class CharRange>
```

> Username: vinniefalco  
> Created_at: 2022-01-05 16:04:02 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r778941631  

What is this CharRange business?

> Username: grisumbras  
> Created_at: 2022-02-28 15:29:04 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r815993482  

A range (e.g. `string_view`) whose `value_type` is `char`. I need this as a template for non-allocating implementation of pointer token look up.


---

## Review 43 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-05 16:18:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/575#pullrequestreview-844795268  

📁 test/error.cpp

```diff
  73 |+         check(condition::pointer_use_error, error::not_found);
  74 |+         check(condition::pointer_use_error, error::token_overflow);
  75 |+         check(condition::pointer_use_error, error::past_the_end);
```

> Username: vinniefalco  
> Created_at: 2022-01-05 16:18:46 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r778952998  

do we need this condition? what would a user do with it?

> Username: grisumbras  
> Created_at: 2022-02-28 15:26:33 UTC  
> Url: https://github.com/boostorg/json/pull/575#discussion_r815991156  

There are two reasons finding via pointer can fail: invalid pointer string and referencing an element that doesn't exist. This condition is for the latter.


---

## Comment 44

> Username: cppalliance-bot  
> Created_at: 2022-02-28 15:21:28 UTC  
> Url: https://github.com/boostorg/json/pull/575#issuecomment-1054365190  

An automated preview of the documentation is available at [https://575.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://575.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 45

> Username: cppalliance-bot  
> Created_at: 2022-02-28 16:28:29 UTC  
> Url: https://github.com/boostorg/json/pull/575#issuecomment-1054434786  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest-condensed-fc83b93.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html)

---

## Comment 46

> Username: cppalliance-bot  
> Created_at: 2022-03-02 11:01:23 UTC  
> Url: https://github.com/boostorg/json/pull/575#issuecomment-1056794533  

An automated preview of the documentation is available at [https://575.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://575.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 47

> Username: cppalliance-bot  
> Created_at: 2022-03-02 12:03:24 UTC  
> Url: https://github.com/boostorg/json/pull/575#issuecomment-1056852826  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest-condensed-95bc1ac.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/575/pullrequest.html)

---
