# #114 Flush buffer after parsing escapes [Closed]

> Username: sdkrystian  
> Created at: 2020-06-20 22:18:33 UTC  
> Updated at: 2020-07-01 03:12:54 UTC  
> Closed at: 2020-06-30 19:54:04 UTC  
> Url: https://github.com/boostorg/json/pull/114  

Fixes #113

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-20 22:37:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/114#pullrequestreview-434457655  

📁 include/boost/json/basic_parser.hpp

```diff
 736 |         st_.pop(st);
 737 |-         switch(st)
 737 |+         if (st != state::str1)
```

> Username: vinniefalco  
> Created_at: 2020-06-20 22:37:04 UTC  
> Updated_at: 2020-06-21 03:49:23 UTC  
> Url: https://github.com/boostorg/json/pull/114#discussion_r443164965  

This is now irregular (different from all the other switch-restore stanzas in the file).

> Username: sdkrystian  
> Created_at: 2020-06-20 22:49:48 UTC  
> Updated_at: 2020-06-21 03:49:23 UTC  
> Url: https://github.com/boostorg/json/pull/114#discussion_r443165585  

The alternative is the keep `state::str1` and manually call trim at each case label


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-20 22:37:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/114#pullrequestreview-434457666  

📁 include/boost/json/basic_parser.hpp

```diff
 816 |+                 // JSON escapes can never make the
 817 |+                 // transcoded utf8 string larger.
 818 |+                 cs.clip(temp.max_size());
```

> Username: vinniefalco  
> Created_at: 2020-06-20 22:37:19 UTC  
> Updated_at: 2020-06-21 03:49:23 UTC  
> Url: https://github.com/boostorg/json/pull/114#discussion_r443164975  

Why `max_size` instead of `capacity`?


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-20 22:38:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/114#pullrequestreview-434457707  

📁 include/boost/json/basic_parser.hpp

```diff
1097 |-         if(BOOST_JSON_LIKELY(more_))
1103 |+ 
1104 |+         // flush if the stream is empty
```

> Username: vinniefalco  
> Created_at: 2020-06-20 22:38:24 UTC  
> Updated_at: 2020-06-21 03:49:23 UTC  
> Url: https://github.com/boostorg/json/pull/114#discussion_r443165024  

LOL the comment is not only unhelpful, but wrong. It should say "buffer" not "stream", and it should say "not empty."

> Username: sdkrystian  
> Created_at: 2020-06-20 22:45:16 UTC  
> Updated_at: 2020-06-21 03:49:23 UTC  
> Url: https://github.com/boostorg/json/pull/114#discussion_r443165363  

Hah, this proves exactly why this comment is needed. If you look at the `if` statement above, if tests `*cs`, meaning that this statement is only executed when the stream is empty :)


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-20 22:40:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/114#pullrequestreview-434457790  

📁 include/boost/json/basic_parser.hpp

```diff
1120 |+             temp.clear();
1121 |         }
1122 |+         // if the stream was empty because it was clipped,
```

> Username: vinniefalco  
> Created_at: 2020-06-20 22:40:19 UTC  
> Updated_at: 2020-06-21 03:49:23 UTC  
> Url: https://github.com/boostorg/json/pull/114#discussion_r443165117  

is empty


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-20 22:41:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/114#pullrequestreview-434457824  

📁 test/basic_parser.cpp

```diff
 783 |+     {
 784 |+         string_view s = 
 785 |+             "\"\\r\\n section id='description'>\\r\\nAll        mbers form the uncountable set "
```

> Username: vinniefalco  
> Created_at: 2020-06-20 22:41:10 UTC  
> Updated_at: 2020-06-21 03:49:23 UTC  
> Url: https://github.com/boostorg/json/pull/114#discussion_r443165176  

If this is from the issue can we please use a raw string so we don't have to have all these escapes?

> Username: sdkrystian  
> Created_at: 2020-06-20 22:47:02 UTC  
> Updated_at: 2020-06-21 03:49:23 UTC  
> Url: https://github.com/boostorg/json/pull/114#discussion_r443165453  

I used escapes to ensure that the strings are identical (i.e. no extra newlines)


---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2020-06-20 23:28:34 UTC  
> Updated_at: 2020-06-21 07:38:02 UTC  
> Url: https://github.com/boostorg/json/pull/114#issuecomment-647056818  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/114?src=pr&el=h1) Report  
> Merging [#114](https://codecov.io/gh/CPPAlliance/json/pull/114?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/9b184b4dc4a39630e9ae6892f69b16f96c91bf3a&el=desc) will **increase** coverage by `0.16%`.  
> The diff coverage is `93.93%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/114/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/114?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #114      +/-   ##  
===========================================  
+ Coverage    98.99%   99.15%   +0.16%       
===========================================  
  Files           59       60       +1       
  Lines         5183     5223      +40       
===========================================  
+ Hits          5131     5179      +48       
+ Misses          52       44       -8       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/114?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/114/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `99.21% <93.93%> (+<0.01%)` | :arrow_up: |  
| [include/boost/json/value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/114/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/buffer.hpp](https://codecov.io/gh/CPPAlliance/json/pull/114/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9idWZmZXIuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/to\_value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/114/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3RvX3ZhbHVlLmhwcA==) | | |  
| [include/boost/json/detail/value\_cast.hpp](https://codecov.io/gh/CPPAlliance/json/pull/114/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9jYXN0LmhwcA==) | | |  
| [include/boost/json/value\_cast.hpp](https://codecov.io/gh/CPPAlliance/json/pull/114/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX2Nhc3QuaHBw) | | |  
| [include/boost/json/detail/to\_value.hpp](https://codecov.io/gh/CPPAlliance/json/pull/114/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC90b192YWx1ZS5ocHA=) | | |  
| [include/boost/json/value\_to.hpp](https://codecov.io/gh/CPPAlliance/json/pull/114/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3RvLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/CPPAlliance/json/pull/114/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/detail/value\_traits.hpp](https://codecov.io/gh/CPPAlliance/json/pull/114/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90cmFpdHMuaHBw) | `100.00% <0.00%> (ø)` | |  
| ... and [4 more](https://codecov.io/gh/CPPAlliance/json/pull/114/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/114?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/114?src=pr&el=footer). Last update [9b184b4...d8496eb](https://codecov.io/gh/CPPAlliance/json/pull/114?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
