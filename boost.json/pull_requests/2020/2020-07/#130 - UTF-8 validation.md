# #130 UTF-8 validation [Merged]

> Username: sdkrystian  
> Created at: 2020-07-16 17:53:19 UTC  
> Updated at: 2020-08-22 18:00:53 UTC  
> Merged at: 2020-07-28 20:02:15 UTC  
> Closed at: 2020-07-28 20:02:15 UTC  
> Url: https://github.com/boostorg/json/pull/130  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-16 17:55:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/130#pullrequestreview-450083298  

📁 include/boost/json/basic_parser.hpp

```diff
  59 |+     utf10, utf11, utf12, 
  60 |+     utf13, utf14, utf15, 
  61 |+     utf16, utf17, utf18,
```

> Username: vinniefalco  
> Created_at: 2020-07-16 17:55:36 UTC  
> Updated_at: 2020-07-28 18:29:26 UTC  
> Url: https://github.com/boostorg/json/pull/130#discussion_r455968628  

column alignment TRIGGERS ME!!

> Username: sdkrystian  
> Created_at: 2020-07-16 18:02:02 UTC  
> Updated_at: 2020-07-28 18:29:26 UTC  
> Url: https://github.com/boostorg/json/pull/130#discussion_r455972465  

Hah, I tried

> Username: vinniefalco  
> Created_at: 2020-07-18 21:07:58 UTC  
> Updated_at: 2020-07-28 18:29:26 UTC  
> Url: https://github.com/boostorg/json/pull/130#discussion_r456828098  

```  
    utf1,  utf2,  utf3,   
    utf4,  utf5,  utf6,  
    utf7,  utf8,  utf9,   
    utf10, utf11, utf12,   
    utf13, utf14, utf15,   
    utf16, utf17, utf18,  
```


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-07-17 01:31:47 UTC  
> Updated_at: 2020-07-28 19:07:48 UTC  
> Url: https://github.com/boostorg/json/pull/130#issuecomment-659775890  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/130?src=pr&el=h1) Report  
> Merging [#130](https://codecov.io/gh/CPPAlliance/json/pull/130?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/53cd3069a455f197bcfda9418c2fa2f7949b13a8&el=desc) will **decrease** coverage by `0.25%`.  
> The diff coverage is `95.54%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/130/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/130?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #130      +/-   ##  
===========================================  
- Coverage    98.04%   97.78%   -0.26%       
===========================================  
  Files           60       62       +2       
  Lines         5311     5746     +435       
===========================================  
+ Hits          5207     5619     +412       
- Misses         104      127      +23       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/130?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/130/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `0.00% <0.00%> (ø)` | |  
| [include/boost/json/impl/parser.ipp](https://codecov.io/gh/CPPAlliance/json/pull/130/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2VyLmlwcA==) | `97.99% <61.53%> (-1.67%)` | :arrow_down: |  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/130/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `98.44% <97.04%> (-0.78%)` | :arrow_down: |  
| [include/boost/json/detail/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/130/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/sse2.hpp](https://codecov.io/gh/CPPAlliance/json/pull/130/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zc2UyLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/parse\_options.hpp](https://codecov.io/gh/CPPAlliance/json/pull/130/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlX29wdGlvbnMuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/buffer.hpp](https://codecov.io/gh/CPPAlliance/json/pull/130/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9idWZmZXIuaHBw) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/130?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/130?src=pr&el=footer). Last update [3ac73ec...95b499a](https://codecov.io/gh/CPPAlliance/json/pull/130?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-25 20:23:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/130#pullrequestreview-455313801  

📁 include/boost/json/detail/basic_parser.hpp

```diff
 134 |     template<bool StackEmpty, bool AllowComments,
 131 |-         bool AllowTrailing, class Handler>
 135 |+         bool AllowTrailing, bool NoValidate, class Handler>
```

> Username: vinniefalco  
> Created_at: 2020-07-25 20:23:50 UTC  
> Updated_at: 2020-07-28 18:29:26 UTC  
> Url: https://github.com/boostorg/json/pull/130#discussion_r460443210  

`AllowInvalid` - see the pattern? :)


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-25 20:26:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/130#pullrequestreview-455313940  

📁 include/boost/json/parse_options.hpp

```diff
  37 |         : allow_comments(comments)
  38 |         , allow_trailing_commas(commas)
  39 |+         , skip_utf8_validation(no_validate)
```

> Username: vinniefalco  
> Created_at: 2020-07-25 20:26:41 UTC  
> Updated_at: 2020-07-28 18:29:26 UTC  
> Url: https://github.com/boostorg/json/pull/130#discussion_r460443453  

Something like  
```  
    parse_options(  
        bool allow_comments_ = false, 	        bool comments = false,   
        bool allow_trailing_ = false) noexcept	        bool commas = false,  
        bool allow_invalid_utf8_ = false) noexcept  
        : allow_comments(allow_comments_)  
        , allow_trailing(allow_trailing_)  
        , allow_invalid_utf8(allow_invalid_utf8_)  
```


---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2020-07-27 22:51:28 UTC  
> Url: https://github.com/boostorg/json/pull/130#issuecomment-664677995  

An automated preview of the documentation is available at [http://130.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://130.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---
