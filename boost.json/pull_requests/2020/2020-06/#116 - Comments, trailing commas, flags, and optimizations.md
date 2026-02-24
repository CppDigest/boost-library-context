# #116 Comments, trailing commas, flags, and optimizations [Closed]

> Username: sdkrystian  
> Created at: 2020-06-28 02:31:43 UTC  
> Updated at: 2020-08-22 18:00:56 UTC  
> Closed at: 2020-07-22 22:43:05 UTC  
> Url: https://github.com/boostorg/json/pull/116  



---

## Review 1 [Commented]

> Username: sdkrystian  
> Created_at: 2020-06-28 02:36:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/116#pullrequestreview-438737997  

📁 include/boost/json/detail/sse2.hpp

```diff
   2 | // Copyright (c) 2019 Peter Dimov (pdimov at gmail dot com),
   3 | //                    Vinnie Falco (vinnie.falco@gmail.com)
   4 |+ // Copyright (c) 2020 Krystian Stasiowski (sdkrystian@gmail.com)
```

> Username: sdkrystian  
> Created_at: 2020-06-28 02:36:44 UTC  
> Updated_at: 2020-07-22 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/116#discussion_r446591367  

Oops, forgot to delete this


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-06-30 18:22:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/116#pullrequestreview-440276466  

📁 bench/bench.cpp

```diff
 322 |         {
 323 |-             monotonic_resource mr;
 323 |+             monotonic_resource mr(1024 * 64);
```

> Username: vinniefalco  
> Created_at: 2020-06-30 18:22:46 UTC  
> Updated_at: 2020-07-22 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/116#discussion_r447890778  

Does this make a difference?

> Username: vinniefalco  
> Created_at: 2020-06-30 18:23:17 UTC  
> Updated_at: 2020-07-22 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/116#discussion_r447891062  

I think this should be in its own commit because it affects performance, and it is currently buried in a larger optimization commit.

> Username: sdkrystian  
> Created_at: 2020-07-16 15:23:06 UTC  
> Updated_at: 2020-07-22 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/116#discussion_r455870564  

Copy

> Username: sdkrystian  
> Created_at: 2020-07-16 15:24:43 UTC  
> Updated_at: 2020-07-22 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/116#discussion_r455871745  

The difference made is negligible -- I added this to make our benchmark more similar to rapidjson


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-06 14:44:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/116#pullrequestreview-443141830  

📁 bench/bench.cpp

```diff
 378 |         bool on_array_begin(error_code&) { return true; }
 379 |-         bool on_array_end(std::size_t, error_code&) { return true; }
 379 |+         bool on_array_end(error_code&) { return true; }
```

> Username: vinniefalco  
> Created_at: 2020-07-06 14:44:14 UTC  
> Updated_at: 2020-07-22 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/116#discussion_r450271455  

Removal of the `size_t` should be in its own commit


---

## Comment 4

> Username: sdkrystian  
> Created_at: 2020-07-06 23:32:12 UTC  
> Url: https://github.com/boostorg/json/pull/116#issuecomment-654514200  

@vinniefalco when this is merged just cherry-pick the "Comment and trailing comma support" commit after merging the previous two in the other PR

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2020-07-20 19:30:46 UTC  
> Url: https://github.com/boostorg/json/pull/116#issuecomment-661289309  

1. The variable name for parse options should be `opt` or `opt_`.  
  
2. There are merge conflicts, please rebase

---

## Comment 6

> Username: sdkrystian  
> Created_at: 2020-07-20 20:27:29 UTC  
> Url: https://github.com/boostorg/json/pull/116#issuecomment-661315663  

@vinniefalco Done and done

---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-20 20:39:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/116#pullrequestreview-451942158  

📁 include/boost/json/detail/basic_parser.hpp

```diff
 108 |     bool complete_ = false; // true on complete parse
 109 |     bool is_key_;
 110 |+     parse_options opt_ = {false, false};
```

> Username: vinniefalco  
> Created_at: 2020-07-20 20:39:09 UTC  
> Updated_at: 2020-07-22 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/116#discussion_r457677869  

But... `parse_options` already has these set to false in the default ctor?

> Username: sdkrystian  
> Created_at: 2020-07-20 22:27:52 UTC  
> Updated_at: 2020-07-22 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/116#discussion_r457726984  

This is a bit of a dilemma -- while `parser` does this, the default constructor for `basic_parser` will not. We can change it though


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-20 20:43:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/116#pullrequestreview-451944736  

📁 include/boost/json/parse_options.hpp

```diff
  20 |+ };
  21 |+ 
  22 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2020-07-20 20:43:16 UTC  
> Updated_at: 2020-07-22 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/116#discussion_r457679961  

missing newline


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-20 20:43:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/116#pullrequestreview-451944862  

📁 include/boost/json/parse_options.hpp

```diff
  11 |+ #define BOOST_JSON_PARSE_OPTIONS_HPP
  12 |+ 
  13 |+ /** Parser Extensions.
```

> Username: vinniefalco  
> Created_at: 2020-07-20 20:43:27 UTC  
> Updated_at: 2020-07-22 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/116#discussion_r457680067  

There will eventually need to be more written here

> Username: vinniefalco  
> Created_at: 2020-07-20 20:45:37 UTC  
> Updated_at: 2020-07-22 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/116#discussion_r457681184  

You could say "the default parse options allow only strict JSON."

> Username: sdkrystian  
> Created_at: 2020-07-22 18:09:46 UTC  
> Updated_at: 2020-07-22 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/116#discussion_r458986846  

I've added a bit.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-20 20:49:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/116#pullrequestreview-451947979  

📁 include/boost/json/basic_parser.hpp

```diff
  50 |+     com4, com5, com6, 
  51 |+     com7, com8, com9, 
  52 |+     com10, com11, com12,
```

> Username: vinniefalco  
> Created_at: 2020-07-20 20:49:16 UTC  
> Updated_at: 2020-07-22 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/116#discussion_r457683009  

TRIGGERED!


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2020-07-20 20:53:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/116#pullrequestreview-451951352  

📁 include/boost/json/basic_parser.hpp

```diff
 472 |+             r = parse_value<StackEmpty, false, true>(h, cs);
 473 |+         else
 474 |+             r = parse_value<StackEmpty, false, false>(h, cs);
```

> Username: vinniefalco  
> Created_at: 2020-07-20 20:53:33 UTC  
> Updated_at: 2020-07-22 18:16:53 UTC  
> Url: https://github.com/boostorg/json/pull/116#discussion_r457685204  

I have to insist on braces, e.g.  
```  
if (opt_.allow_comments)  
{  
  if (opt_.allow_trailing_commas)  
...  
```


---

## Comment 12

> Username: vinniefalco  
> Created_at: 2020-07-22 00:30:01 UTC  
> Url: https://github.com/boostorg/json/pull/116#issuecomment-662174463  

Still failing

---

## Comment 13

> Username: codecov[bot]  
> Created_at: 2020-07-22 22:19:13 UTC  
> Updated_at: 2020-07-22 22:19:15 UTC  
> Url: https://github.com/boostorg/json/pull/116#issuecomment-662726067  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/116?src=pr&el=h1) Report  
> Merging [#116](https://codecov.io/gh/CPPAlliance/json/pull/116?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/53cd3069a455f197bcfda9418c2fa2f7949b13a8&el=desc) will **decrease** coverage by `0.38%`.  
> The diff coverage is `89.35%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/116/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/116?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #116      +/-   ##  
===========================================  
- Coverage    98.04%   97.65%   -0.39%       
===========================================  
  Files           60       62       +2       
  Lines         5311     5466     +155       
===========================================  
+ Hits          5207     5338     +131       
- Misses         104      128      +24       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/116?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/116/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9iYXNpY19wYXJzZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/116/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `0.00% <0.00%> (ø)` | |  
| [include/boost/json/impl/parser.ipp](https://codecov.io/gh/CPPAlliance/json/pull/116/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2VyLmlwcA==) | `97.99% <61.53%> (-1.67%)` | :arrow_down: |  
| [include/boost/json/basic\_parser.hpp](https://codecov.io/gh/CPPAlliance/json/pull/116/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlci5ocHA=) | `98.01% <92.85%> (-1.21%)` | :arrow_down: |  
| [include/boost/json/parse\_options.hpp](https://codecov.io/gh/CPPAlliance/json/pull/116/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlX29wdGlvbnMuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/116?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/116?src=pr&el=footer). Last update [53cd306...73fc410](https://codecov.io/gh/CPPAlliance/json/pull/116?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
