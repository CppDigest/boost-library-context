# #634 urls::format [Merged]

> Username: alandefreitas  
> Created at: 2022-12-06 21:34:33 UTC  
> Updated at: 2023-07-24 14:36:34 UTC  
> Merged at: 2022-12-29 22:54:46 UTC  
> Closed at: 2022-12-29 22:54:46 UTC  
> Url: https://github.com/boostorg/url/pull/634  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-12-06 21:44:46 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1340050865  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-06 22:10:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1207409557  

📁 include/boost/url/experimental/detail/impl/url_template.ipp

```diff
 123 |+                 *it != '}')
 124 |+                 ++it;
 125 |+             BOOST_ASSERT(it != end);
```

> Username: vinniefalco  
> Created_at: 2022-12-06 22:10:46 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041512210  

This assert is weird, shouldn't you write the loop this way  
```  
while (*it != '}' )  
{  
    ++it;  
    BOOST_ASSERT( it != end );  
}  
```


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-06 22:13:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1207412133  

📁 include/boost/url/experimental/detail/vformat.hpp

```diff
  15 |+ #include <boost/url/url.hpp>
  16 |+ #include <boost/url/experimental/detail/format_args.hpp>
  17 |+ #include <functional>
```

> Username: vinniefalco  
> Created_at: 2022-12-06 22:13:19 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041514142  

is there way to avoid including this header?

> Username: alandefreitas  
> Created_at: 2022-12-06 22:33:04 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041535348  

I think it's worth including it because we would be replicating most of the functionality if we didn't.

> Username: vinniefalco  
> Created_at: 2022-12-06 22:42:47 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041542425  

Do users end up seeing it? What do we need from it?

> Username: alandefreitas  
> Created_at: 2022-12-06 22:52:35 UTC  
> Updated_at: 2022-12-06 22:52:36 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041550079  

When implementing our mini-fmtlib, the format `args` are type-erased with a `handle` type that can format the string for whatever the original type was. Then I used `std::function` for that because a custom type would be 90% the same as `std::function`.   
  
However, because `function` is being used to type erase the formatter, they are always the same types. That means we can get rid of any extra compile time spent on instantiations with `extern template` in the `ipp` file.

> Username: vinniefalco  
> Created_at: 2022-12-06 23:11:02 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041565878  

no I don't like extern template. If users don't see functional included, then its fine.

> Username: alandefreitas  
> Created_at: 2022-12-06 23:25:23 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041576873  

I'll think about it but I'm not sure how I can hide that yet. Unless I reimplement my own version of the `function<...>` overloads I need.

> Username: klemens-morgenstern  
> Created_at: 2022-12-07 07:42:53 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041861228  

What's wrong with `extern template` ?

> Username: vinniefalco  
> Created_at: 2022-12-07 19:38:49 UTC  
> Updated_at: 2022-12-07 19:38:50 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1042607441  

This can be done with a plain function pointer instead of `std::function` but also it could use an `initializer_list` to avoid the need to allocate memory. Follow me for more C++ Tips.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-06 22:14:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1207412930  

📁 include/boost/url/experimental/format.hpp

```diff
   9 |+ 
  10 |+ #ifndef BOOST_URL_EXPERIMENTAL_FORMAT_HPP
  11 |+ #define BOOST_URL_EXPERIMENTAL_FORMAT_HPP
```

> Username: vinniefalco  
> Created_at: 2022-12-06 22:14:07 UTC  
> Updated_at: 2022-12-06 22:14:08 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041514705  

This should not be in "experimental" since it is not an experimental feature. And all new public includes need to be added to `<boost/url.hpp>`


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-06 22:18:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1207419602  

📁 include/boost/url/experimental/detail/impl/url_template.ipp

```diff
 185 |+         if (scheme.starts_with('{'))
 186 |+         {
 187 |+             url_base::op_t op(u);
```

> Username: vinniefalco  
> Created_at: 2022-12-06 22:18:59 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041518820  

If create and destroy an instance of `opt_t` more than once in a function, then you will get the Basic exception guarantee and not the Strong.

> Username: alandefreitas  
> Created_at: 2022-12-06 22:34:43 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041536406  

I'll do that anyway but a single `opt_t` won't get us a strong guarantee either, right?

> Username: vinniefalco  
> Created_at: 2022-12-07 15:25:11 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1042346487  

yes it does


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-06 22:22:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1207423271  

📁 include/boost/url/experimental/detail/formatter.hpp

```diff
  39 |+     char*& out,
  40 |+     char c,
  41 |+     CharSet const& unreserved)
```

> Username: vinniefalco  
> Created_at: 2022-12-06 22:22:52 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041521625  

well this could be `lut_chars const&`

> Username: alandefreitas  
> Created_at: 2022-12-06 22:35:20 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041536913  

Is `lut_chars` more efficient than type-erasing the function?

> Username: vinniefalco  
> Created_at: 2022-12-06 22:43:30 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041542876  

Yes very much so, because I think you are only passing variables of type `lut_chars` in anyway. And since this function is not public, it doesn't need to work with arbitrary _CharSet_ types.

> Username: vinniefalco  
> Created_at: 2022-12-06 22:44:03 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041543281  

well at least remove the parameter name so it is clear that it is unused

> Username: alandefreitas  
> Created_at: 2022-12-06 22:47:17 UTC  
> Updated_at: 2022-12-06 22:47:18 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041545793  

`cs` is unused?

> Username: vinniefalco  
> Created_at: 2022-12-06 23:10:17 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041565269  

sorry, in the digits formatter thing


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-06 22:23:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1207424172  

📁 include/boost/url/experimental/detail/formatter.hpp

```diff
 201 |+         {
 202 |+             int d = v / p;
 203 |+             encode_one(out, '0' + d, cs);
```

> Username: vinniefalco  
> Created_at: 2022-12-06 22:23:44 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041522211  

but we don't need the character set here? for digits?

> Username: alandefreitas  
> Created_at: 2022-12-06 22:39:47 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1041540407  

The charset is a parameter because we don't know in what component the string will be formatted.   
  
Maybe we could consider we don't need to generalize the algorithm that much because digits are accepted unencoded in all components so we could bypass the `cs` here and the same could be said about other combinations of component/type/charset.   
  
I'm not sure removing the general algorithm is worth it though. The code might become more difficult to follow without a big benefit.


---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-12-07 15:19:51 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1341122791  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-12-07 19:42:11 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1341498091  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-12-07 20:20:11 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1341541520  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-12-07 20:51:18 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1341580211  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-12-07 21:14:06 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1341606191  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 13

> Username: alandefreitas  
> Created_at: 2022-12-07 22:32:15 UTC  
> Updated_at: 2022-12-08 01:16:16 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1341679304  

note to self:   
  
- there are useful ideas in peter's implementation: https://godbolt.org/z/d7cTnYPbY  
- we can have a single `format` function  
- compare this implementation with:  
    - https://github.com/Tinkoff/uri-template  
    - https://www.rfc-editor.org/rfc/rfc6570

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2022-12-08 00:40:33 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1341808886  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2022-12-08 15:44:52 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1342926438  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 16

> Username: codecov[bot]  
> Created_at: 2022-12-08 15:51:31 UTC  
> Updated_at: 2022-12-29 21:04:56 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1342934511  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/634?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#634](https://codecov.io/gh/boostorg/url/pull/634?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d8df7d7) into [develop](https://codecov.io/gh/boostorg/url/commit/cd71d5e9f6e5af38d93ef2dcd3252a4d24f6d233?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cd71d5e) will **increase** coverage by `0.45%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head d8df7d7 differs from pull request most recent head 239219a. Consider uploading reports for the commit 239219a to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/634/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/634?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #634      +/-   ##  
===========================================  
+ Coverage    96.42%   96.88%   +0.45%       
===========================================  
  Files          139      147       +8       
  Lines         6946     7924     +978       
===========================================  
+ Hits          6698     7677     +979       
+ Misses         248      247       -1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/634?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/rfc/impl/query\_rule.ipp](https://codecov.io/gh/boostorg/url/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvcXVlcnlfcnVsZS5pcHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_base.hpp](https://codecov.io/gh/boostorg/url/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view\_base.hpp](https://codecov.io/gh/boostorg/url/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/format\_args.hpp](https://codecov.io/gh/boostorg/url/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2Zvcm1hdF9hcmdzLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/impl/format\_args.hpp](https://codecov.io/gh/boostorg/url/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvZm9ybWF0X2FyZ3MuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/impl/format\_args.ipp](https://codecov.io/gh/boostorg/url/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvZm9ybWF0X2FyZ3MuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/impl/pattern.ipp](https://codecov.io/gh/boostorg/url/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvcGF0dGVybi5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/impl/pct\_format.ipp](https://codecov.io/gh/boostorg/url/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvcGN0X2Zvcm1hdC5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [...e/boost/url/detail/impl/replacement\_field\_rule.ipp](https://codecov.io/gh/boostorg/url/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvcmVwbGFjZW1lbnRfZmllbGRfcnVsZS5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/vformat.hpp](https://codecov.io/gh/boostorg/url/pull/634/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3Zmb3JtYXQuaHBw) | `100.00% <100.00%> (ø)` | |  
| ... and [5 more](https://codecov.io/gh/boostorg/url/pull/634/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/634?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/634?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [cd71d5e...239219a](https://codecov.io/gh/boostorg/url/pull/634?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2022-12-08 19:54:24 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1343276449  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2022-12-09 18:09:41 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1344611146  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2022-12-10 20:24:26 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1345377683  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2022-12-12 22:29:55 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1347432585  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2022-12-13 00:15:34 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1347561065  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2022-12-13 00:34:17 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1347576892  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2022-12-13 22:20:10 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1349887666  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2022-12-14 01:25:36 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1350216963  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2022-12-14 01:37:35 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1350224202  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2022-12-15 05:25:22 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1352576432  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 27

> Username: cppalliance-bot  
> Created_at: 2022-12-15 16:57:03 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1353399747  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 28

> Username: cppalliance-bot  
> Created_at: 2022-12-16 02:04:35 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1354075891  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 29

> Username: cppalliance-bot  
> Created_at: 2022-12-16 15:44:01 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1355107346  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 30

> Username: cppalliance-bot  
> Created_at: 2022-12-16 15:55:12 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1355128568  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-16 15:59:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1221084060  

📁 include/boost/url/detail/replacement_id_rule.hpp

```diff
  33 |+     using value_type = string_view;
  34 |+ 
  35 |+     BOOST_URL_DECL
```

> Username: vinniefalco  
> Created_at: 2022-12-16 15:59:36 UTC  
> Updated_at: 2022-12-16 15:59:37 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050911033  

is this function called from user-visible code? If not, it doesn't need the dll export.

> Username: alandefreitas  
> Created_at: 2022-12-16 16:09:48 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050920859  

We had an error in win32 because it's called from code header files I think. I don't remember which. I'll check.


---

## Review 32 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-16 15:59:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1221084251  

📁 include/boost/url/detail/replacement_id_rule.hpp

```diff
  38 |+         char const*& it,
  39 |+         char const* end
  40 |+     ) const noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-12-16 15:59:46 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050911166  

no I dont like this style please fix it

> Username: alandefreitas  
> Created_at: 2022-12-16 16:03:10 UTC  
> Updated_at: 2022-12-16 16:03:11 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050914411  

You want  
  
```cpp  
    auto  
    parse(  
        char const*& it,  
        char const* const end  
            ) const noexcept ->  
        result<value_type>;  
```  
  
as in `hier_part_rule`?


---

## Review 33 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-16 16:00:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1221085485  

📁 include/boost/url/rfc/detail/impl/hier_part_rule.ipp

```diff
  72 |+          (*it != '/' &&
  73 |+           *it != '?' &&
  74 |+           *it != '#')))
```

> Username: vinniefalco  
> Created_at: 2022-12-16 16:00:37 UTC  
> Updated_at: 2022-12-16 16:00:38 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050911976  

This fix needs to go into develop as its own commit?

> Username: alandefreitas  
> Created_at: 2022-12-16 16:14:58 UTC  
> Updated_at: 2022-12-16 16:15:05 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050925476  

This minor fix was getting in the way of this PR so I fixed it. I can not squash this commit if you want. Or undo it at the end and open another PR.

> Username: vinniefalco  
> Created_at: 2022-12-17 00:40:36 UTC  
> Updated_at: 2022-12-17 00:40:37 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1051267080  

Its fine to fix it in the PR and also submit a separate PR to patch it in develop, git is smart enough to elide it when you squash and rebase.


---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-16 16:01:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1221086735  

📁 include/boost/url/url_base.hpp

```diff
  36 | struct params_iter_impl;
  37 | struct segments_iter_impl;
  38 |+ struct url_template;
```

> Username: vinniefalco  
> Created_at: 2022-12-16 16:01:32 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050912838  

maybe we can rename `url_template` to `pattern`?

> Username: alandefreitas  
> Created_at: 2022-12-19 17:13:51 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1052447260  

I'm fine with both. `pattern` seems a little generalistic but I'm assuming you don't like `url_pattern`. It's an implementation detail anyway so we can change that as much as we want.


---

## Review 35 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-16 16:02:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1221087981  

📁 include/boost/url/detail/format_args.hpp

```diff
  39 |+ // state of the format string. It basically keeps
  40 |+ // track of where we are in the format string.
  41 |+ class format_parse_context
```

> Username: vinniefalco  
> Created_at: 2022-12-16 16:02:23 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050913634  

`format_pos`?

> Username: pdimov  
> Created_at: 2022-12-16 16:30:10 UTC  
> Updated_at: 2022-12-16 16:30:11 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050940127  

format_parse_context is the standard name.


---

## Review 36 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-16 16:03:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1221089750  

📁 include/boost/url/detail/format_args.hpp

```diff
 116 |+     void const* arg_;
 117 |+     void (*measure_)( format_parse_context&, measure_context&, grammar::lut_chars const&, void const* );
 118 |+     void (*fmt_)( format_parse_context& pctx, format_context& fctx, grammar::lut_chars const&, void const* );
```

> Username: vinniefalco  
> Created_at: 2022-12-16 16:03:27 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050914695  

Why are these lines so long


---

## Review 37 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-16 16:03:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1221090326  

📁 include/boost/url/detail/format_args.hpp

```diff
 119 |+     string_view name_;
 120 |+ 
 121 |+     template <class A>
```

> Username: vinniefalco  
> Created_at: 2022-12-16 16:03:53 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050915106  

why is this a template?

> Username: pdimov  
> Created_at: 2022-12-16 16:30:50 UTC  
> Updated_at: 2022-12-16 16:30:51 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050940735  

Because it needs to be.


---

## Review 38 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-16 16:06:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1221093393  

📁 include/boost/url/detail/format_args.hpp

```diff
1046 |+     invert(T&, std::false_type)
1047 |+     {}
1048 |+ };
```

> Username: vinniefalco  
> Created_at: 2022-12-16 16:06:04 UTC  
> Updated_at: 2022-12-16 16:06:05 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050917192  

1000+ line header file, is this visible to users? Or is it only included from a .ipp file?

> Username: alandefreitas  
> Created_at: 2022-12-19 18:40:27 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1052518376  

Most content has been moved to ipp files now.


---

## Review 39 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-16 16:06:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1221093726  

📁 include/boost/url/detail/impl/pct_format.ipp

```diff
  21 |+     grammar::lut_chars const& cs,
  22 |+     format_parse_context& pctx,
  23 |+     measure_context& mctx)
```

> Username: vinniefalco  
> Created_at: 2022-12-16 16:06:19 UTC  
> Updated_at: 2022-12-16 16:06:20 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050917433  

are these two contexts always passed together?

> Username: alandefreitas  
> Created_at: 2022-12-19 18:41:46 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1052519544  

It's sometimes `format_parse_context` and `format_context` as well.


---

## Review 40 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-16 16:10:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1221099302  

📁 include/boost/url/detail/impl/url_template.ipp

```diff
  28 |+ #include <boost/url/rfc/detail/fragment_part_rule.hpp>
  29 |+ #include <boost/url/rfc/detail/host_rule.hpp>
  30 |+ #include <boost/url/rfc/ipv4_address_rule.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-12-16 16:10:09 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050921153  

this is not in alphabetical order


---

## Review 41 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-16 16:11:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1221101098  

📁 include/boost/url/detail/impl/url_template.ipp

```diff
 961 |+ } // boost
 962 |+ 
 963 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2022-12-16 16:11:31 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050922381  

ugh.. another thousand-liner

> Username: alandefreitas  
> Created_at: 2022-12-19 18:49:24 UTC  
> Updated_at: 2022-12-19 18:49:25 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1052525560  

This one is an `.ipp` file though. No intermediary rules for the pattern are included in public headers.   
  
Anyway, the file contains:  
  
- pattern_rule (172 lines)  
- intermediate rules for pattern_rule (334 lines)  
- function to apply a pattern to a url_base (305 lines)  
  
The rules are quite long because they are basically implementing more complex versions of each url rule.   
  
To what level of granularity should I break this?


---

## Review 42 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-16 16:11:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1221101385  

📁 include/boost/url/detail/pct_format.hpp

```diff
  11 |+ #define BOOST_URL_DETAIL_PCT_FORMAT_HPP
  12 |+ 
  13 |+ #include <boost/url/detail/config.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-12-16 16:11:44 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050922600  

detail files don't need config.hpp


---

## Comment 43

> Username: cppalliance-bot  
> Created_at: 2022-12-16 16:12:07 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1355155418  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Review 44 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-16 16:13:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1221103735  

📁 include/boost/url/detail/replacement_id_rule.hpp

```diff
  11 |+ #define BOOST_URL_DETAIL_REPLACEMENT_ID_RULE_HPP
  12 |+ 
  13 |+ #include <boost/url/detail/config.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-12-16 16:13:32 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050924194  

detail headers dont need config.hpp


---

## Review 45 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-16 16:14:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1221104440  

📁 include/boost/url/detail/replacement_id_rule.hpp

```diff
  22 |+ #include <boost/url/grammar/tuple_rule.hpp>
  23 |+ #include <boost/url/grammar/unsigned_rule.hpp>
  24 |+ #include <boost/url/grammar/variant_rule.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-12-16 16:14:02 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1050924683  

This seems like quite a lot of includes, are you certain every one of them is necessary?


---

## Comment 46

> Username: cppalliance-bot  
> Created_at: 2022-12-16 19:33:26 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1355490087  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 47

> Username: cppalliance-bot  
> Created_at: 2022-12-17 01:04:35 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1355895254  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 48

> Username: cppalliance-bot  
> Created_at: 2022-12-17 22:28:31 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1356489014  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 49

> Username: cppalliance-bot  
> Created_at: 2022-12-17 23:13:44 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1356496429  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 50

> Username: cppalliance-bot  
> Created_at: 2022-12-19 16:27:40 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1357923461  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 51

> Username: cppalliance-bot  
> Created_at: 2022-12-19 17:24:28 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1357997113  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Review 52 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-19 19:15:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1223409832  

📁 include/boost/url/detail/format_args.hpp

```diff
  11 |+ #define BOOST_URL_DETAIL_FORMAT_ARGS_HPP
  12 |+ 
  13 |+ #include <boost/url/detail/config.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-12-19 19:15:23 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1052551012  

you dont have to include config.hpp in non-public headers


---

## Comment 53

> Username: cppalliance-bot  
> Created_at: 2022-12-19 19:22:21 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1358145732  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 54

> Username: alandefreitas  
> Created_at: 2022-12-19 19:30:29 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1358152580  

I renamed `url_template` to `pattern` as recommended in https://github.com/boostorg/url/pull/634#discussion_r1050912838 but I'm now going through https://www.rfc-editor.org/rfc/rfc6570 and it's important to note it calls it "URI templates".

---

## Comment 55

> Username: cppalliance-bot  
> Created_at: 2022-12-19 19:49:02 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1358174912  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 56

> Username: cppalliance-bot  
> Created_at: 2022-12-19 22:34:20 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1358498555  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 57

> Username: cppalliance-bot  
> Created_at: 2022-12-19 23:41:49 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1358604519  

An automated preview of the documentation is available at [https://634.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://634.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 58

> Username: cppalliance-bot  
> Created_at: 2022-12-19 23:49:21 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1358625424  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 59

> Username: cppalliance-bot  
> Created_at: 2022-12-19 23:56:47 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1358640887  

An automated preview of the documentation is available at [https://634.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://634.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 60

> Username: cppalliance-bot  
> Created_at: 2022-12-20 00:01:28 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1358651027  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 61

> Username: cppalliance-bot  
> Created_at: 2022-12-20 21:56:50 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1360374238  

An automated preview of the documentation is available at [https://634.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://634.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 62

> Username: cppalliance-bot  
> Created_at: 2022-12-20 22:04:51 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1360381009  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 63

> Username: cppalliance-bot  
> Created_at: 2022-12-22 01:32:02 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1362285389  

An automated preview of the documentation is available at [https://634.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://634.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 64

> Username: cppalliance-bot  
> Created_at: 2022-12-22 01:40:15 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1362288850  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 65

> Username: cppalliance-bot  
> Created_at: 2022-12-22 20:56:55 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1363332999  

An automated preview of the documentation is available at [https://634.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://634.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 66

> Username: cppalliance-bot  
> Created_at: 2022-12-22 21:07:14 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1363340908  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 67

> Username: alandefreitas  
> Created_at: 2022-12-28 15:44:51 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1366745260  

A small comparison with rfc6570  
  
https://gist.github.com/alandefreitas/cdd2e08042d952c35cb030c4638106ff

---

## Review 68 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-28 19:32:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1231909699  

📁 doc/qbk/0.main.qbk

```diff
  67 | [def __std_string__             [@https://en.cppreference.com/w/cpp/string/basic_string `std::string`]]
  68 | [def __std_tuple__              [@https://en.cppreference.com/w/cpp/utility/tuple `std::tuple`]]
  69 | [def __std_get__                [@https://en.cppreference.com/w/cpp/utility/tuple/get `std::get`]]
```

> Username: vinniefalco  
> Created_at: 2022-12-28 19:32:32 UTC  
> Updated_at: 2022-12-28 19:32:36 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058550285  

not alphabetical


---

## Review 69 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-28 19:33:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1231909899  

📁 doc/qbk/0.main.qbk

```diff
  69 | [def __std_get__                [@https://en.cppreference.com/w/cpp/utility/tuple/get `std::get`]]
  70 | [def __std_string_view__        [@https://en.cppreference.com/w/cpp/string/basic_string_view `std::string_view`]]
  71 |+ [def __std_format_string__      [@https://en.cppreference.com/w/cpp/utility/format/basic_format_string `std::format_string`]]
```

> Username: vinniefalco  
> Created_at: 2022-12-28 19:33:00 UTC  
> Updated_at: 2022-12-28 19:33:01 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058550449  

not alphabetical


---

## Review 70 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-28 19:33:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1231910011  

📁 doc/qbk/3.1.parsing.qbk

```diff
 124 | documentation in [@boost:/libs/system/doc/html/system.html Boost.System].
 125 | 
 126 |+ [heading Formatting]
```

> Username: vinniefalco  
> Created_at: 2022-12-28 19:33:15 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058550567  

Why is "Formatting" in the Parsing section?

> Username: alandefreitas  
> Created_at: 2022-12-28 20:35:33 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058575646  

It can be in the Parsing section, another section after "Parsing", or in the Containers "section"? It does involve parsing the template though.  
  
![image](https://user-images.githubusercontent.com/5369819/209869052-865a9069-c53d-4d77-ab14-3a04096dc405.png)

> Username: vinniefalco  
> Created_at: 2022-12-28 22:31:47 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058631480  

it has nothing to do with parsing.

> Username: vinniefalco  
> Created_at: 2022-12-28 22:32:01 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058631544  

put it after Percent Encoding, as Formatting


---

## Review 71 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-28 19:33:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1231910292  

📁 doc/qbk/quickref.xml

```diff
  83 |+           <member><link linkend="url.ref.boost__urls__format">format</link></member>
  84 |+           <member><link linkend="url.ref.boost__urls__format_to">format_to</link></member>
  85 |+           <member><link linkend="url.ref.boost__urls__arg">arg</link></member>
```

> Username: vinniefalco  
> Created_at: 2022-12-28 19:33:45 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058550811  

This is not in alphabetical order and why did you add `arg`?

> Username: alandefreitas  
> Created_at: 2022-12-28 20:44:00 UTC  
> Updated_at: 2022-12-28 20:44:01 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058578324  

To explain what this public function does?

> Username: vinniefalco  
> Created_at: 2022-12-28 22:32:22 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058631631  

yeah, alphabetical please though


---

## Review 72 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-28 19:36:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1231912133  

📁 include/boost/url/detail/impl/format_args.ipp

```diff
 638 |+ } // detail
 639 |+ } // urls
 640 |+ } // boost
```

> Username: vinniefalco  
> Created_at: 2022-12-28 19:36:41 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058552262  

This is a medium sized file, but at least its in the .ipp and not a visible header.


---

## Review 73 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-28 19:39:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1231913312  

📁 include/boost/url/detail/impl/pattern.ipp

```diff
 944 |+ } // urls
 945 |+ } // boost
 946 |+ 
```

> Username: vinniefalco  
> Created_at: 2022-12-28 19:39:02 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058553052  

big file, but in the .ipp - good


---

## Review 74 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-28 19:39:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1231913540  

📁 include/boost/url/detail/impl/pattern.ipp

```diff
 938 |+ {
 939 |+     return grammar::parse(
 940 |+         s, pattern_rule);
```

> Username: vinniefalco  
> Created_at: 2022-12-28 19:39:33 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058553215  

This is a good function, it hides the rule declarations from the public headers


---

## Review 75 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-28 19:40:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1231914213  

📁 include/boost/url/detail/pattern.hpp

```diff
  46 |+     apply(
  47 |+         url_base& u,
  48 |+         format_args const& args) const;
```

> Username: vinniefalco  
> Created_at: 2022-12-28 19:40:48 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058553757  

Yep this all makes sense


---

## Review 76 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-28 19:41:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1231914451  

📁 include/boost/url/detail/pct_format.hpp

```diff
  19 |+ // pct-encoding. This is used to format
  20 |+ // generalized patterns with multiple replacement
  21 |+ // ids.
```

> Username: vinniefalco  
> Created_at: 2022-12-28 19:41:19 UTC  
> Updated_at: 2022-12-28 19:41:20 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058553927  

useless comment


---

## Review 77 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-28 19:41:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1231914752  

📁 include/boost/url/detail/replacement_field_rule.hpp

```diff
  21 |+ 
  22 |+ // replacement_field ::=  "{" [arg_id] [":" format_spec "}"
  23 |+ struct replacement_field_rule_t
```

> Username: vinniefalco  
> Created_at: 2022-12-28 19:41:57 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058554129  

Do these need to be in this file or could they just go to the .ipp where they are used?

> Username: alandefreitas  
> Created_at: 2022-12-29 17:12:48 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1059065647  

It's kind of both. Only .ipp files include it. But it's in a separate header because many .ipp files use these rules. The router will use them too.


---

## Review 78 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-28 19:43:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1231916019  

📁 include/boost/url/format.hpp

```diff
 363 |+     string_view fmt,
 364 |+ #ifdef BOOST_URL_DOCS
 365 |+     std::initializer_list<__implementation_defined__> args
```

> Username: vinniefalco  
> Created_at: 2022-12-28 19:43:58 UTC  
> Updated_at: 2022-12-28 19:43:59 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058554887  

You could make this look like `Args const&&... args` in the docs if you want, it might be more clear than an init-list of implementation defined things. And that should probably be `__see_below__` instead of `__implementation_defined__`, because you have to document what things the user can pass for args.

> Username: alandefreitas  
> Created_at: 2022-12-28 20:45:27 UTC  
> Updated_at: 2022-12-28 20:45:28 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058578951  

> You could make this look like Args const&&... args in the docs if you want  
  
How would we differentiate that from the `Args const&&... args`?

> Username: vinniefalco  
> Created_at: 2022-12-28 22:33:37 UTC  
> Updated_at: 2022-12-28 22:33:38 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058631975  

oh... does the `initializer_list` overload need to be public?

> Username: alandefreitas  
> Created_at: 2022-12-28 23:39:56 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058650881  

Yes. The user uses it directly.   
  
It's not an implementation detail that emulates `Args const&&... args` accepting initializer_lists.

> Username: vinniefalco  
> Created_at: 2022-12-29 14:24:12 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058981464  

Why are we offering two ways to do the same thing?

> Username: alandefreitas  
> Created_at: 2022-12-29 17:20:15 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1059068919  

Yes. The second overload was suggested by Peter to simplify named arguments.  
  
```cpp  
url u = format("https://{username}.gigantic-server.com:{port}/{basePath}/{path}",  
            arg("basePath", "v2"), arg("path", "index.html"), arg("port", 80), arg("username", "joe"));,  
assert(u.buffer() == "https://joe.gigantic-server.com:80/v2/index.html");  
```  
  
becomes  
  
```cpp  
url u = format("https://{username}.gigantic-server.com:{port}/{basePath}/{path}",  
            {{"basePath", "v2"}, {"path", "index.html"}, {"port", 80}, {"username", "joe"}});  
assert(u.buffer() == "https://joe.gigantic-server.com:80/v2/index.html");  
```  
  
It doesn't make much sense when the arguments are not named. That's one of the discussions we had on slack.  
  
In a way, if there's demand for that, this could converge to some kind of map concept, although that would involve changing other things.   
  
```cpp  
url u = format("https://{username}.gigantic-server.com:{port}/{basePath}/{path}", json_obj_from_openapi);  
assert(u.buffer() == "https://joe.gigantic-server.com:80/v2/index.html");  
```


---

## Review 79 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-28 19:44:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1231916173  

📁 include/boost/url/format.hpp

```diff
 389 |+     @endcode
 390 |+ 
 391 |+     @return A type that temporarily stored a named argument
```

> Username: vinniefalco  
> Created_at: 2022-12-28 19:44:20 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058554984  

Functions can't return types....


---

## Review 80 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-28 19:44:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/634#pullrequestreview-1231916338  

📁 include/boost/url/format.hpp

```diff
 374 |+ }
 375 |+ 
 376 |+ /** Construct a named argument for a replacement field
```

> Username: vinniefalco  
> Created_at: 2022-12-28 19:44:38 UTC  
> Url: https://github.com/boostorg/url/pull/634#discussion_r1058555097  

Instead of "construct a named argument" you can say "designate a named argument"


---

## Comment 81

> Username: vinniefalco  
> Created_at: 2022-12-28 19:45:51 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1366879915  

We can merge this as-is and continue to refine it, addressing my comments and other things. I did not check the functionality but only the physical structure of the code.

---

## Comment 82

> Username: cppalliance-bot  
> Created_at: 2022-12-29 17:36:55 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1367484856  

An automated preview of the documentation is available at [https://634.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://634.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 83

> Username: cppalliance-bot  
> Created_at: 2022-12-29 17:44:10 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1367488228  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 84

> Username: cppalliance-bot  
> Created_at: 2022-12-29 21:11:58 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1367587636  

An automated preview of the documentation is available at [https://634.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://634.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 85

> Username: cppalliance-bot  
> Created_at: 2022-12-29 21:20:09 UTC  
> Url: https://github.com/boostorg/url/pull/634#issuecomment-1367590811  

GCOVR code coverage report [https://634.url.prtest.cppalliance.org/gcovr/index.html](https://634.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://634.url.prtest.cppalliance.org/genhtml/index.html](https://634.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://634.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
