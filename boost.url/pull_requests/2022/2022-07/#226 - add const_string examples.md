# #226 add const_string examples [Closed]

> Username: alandefreitas  
> Created at: 2022-07-07 21:19:45 UTC  
> Updated at: 2022-08-03 21:07:12 UTC  
> Closed at: 2022-08-02 04:36:03 UTC  
> Url: https://github.com/boostorg/url/pull/226  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-07 23:02:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/226#pullrequestreview-1032241864  

📁 CMakeLists.txt

```diff
  43 |     # container comes from json
  45 |-     set(BOOST_INCLUDE_LIBRARIES url json)
  44 |+     set(BOOST_INCLUDE_LIBRARIES url json filesystem)
```

> Username: vinniefalco  
> Created_at: 2022-07-07 23:02:39 UTC  
> Url: https://github.com/boostorg/url/pull/226#discussion_r916339043  

Does this make Boost.URL require filesystem to build? Or is it just for the examples?

> Username: alandefreitas  
> Created_at: 2022-07-08 16:51:13 UTC  
> Updated_at: 2022-07-08 16:51:15 UTC  
> Url: https://github.com/boostorg/url/pull/226#discussion_r916988888  

Just the examples. Since the URL is not allowed to access the parent path. maybe we could remove `filesystem` from the examples too and still make the point about decoding and appending to a string that represents a path.

> Username: vinniefalco  
> Created_at: 2022-07-08 17:23:02 UTC  
> Updated_at: 2022-07-08 17:23:03 UTC  
> Url: https://github.com/boostorg/url/pull/226#discussion_r917010132  

Exploring the interaction between filesystem and URL is important, so we should keep it using `filesystem::path`.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-07 23:02:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/226#pullrequestreview-1032241964  

📁 Jamfile

```diff
  21 | 
  22 | build-project test ;
  23 |+ build-project example ;
```

> Username: vinniefalco  
> Created_at: 2022-07-07 23:02:49 UTC  
> Updated_at: 2022-07-07 23:02:50 UTC  
> Url: https://github.com/boostorg/url/pull/226#discussion_r916339113  

missing newline


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-07 23:03:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/226#pullrequestreview-1032242400  

📁 example/const_string/path.cpp

```diff
  51 |+ 
  52 |+     return EXIT_SUCCESS;
  53 |+ }
```

> Username: vinniefalco  
> Created_at: 2022-07-07 23:03:33 UTC  
> Url: https://github.com/boostorg/url/pull/226#discussion_r916339421  

missing newline


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-07 23:04:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/226#pullrequestreview-1032242841  

📁 example/const_string/path.cpp

```diff
  44 |+     fs::path p = doc_root;
  45 |+     for (urls::const_string seg: u.segments()) {
  46 |+         p /= seg;
```

> Username: vinniefalco  
> Created_at: 2022-07-07 23:04:23 UTC  
> Url: https://github.com/boostorg/url/pull/226#discussion_r916339760  

hey not bad!

> Username: akrzemi1  
> Created_at: 2022-07-08 16:28:06 UTC  
> Updated_at: 2022-07-08 16:28:07 UTC  
> Url: https://github.com/boostorg/url/pull/226#discussion_r916973100  

From the discussions, I got the impression that the way this would be used is:  
  
```c++  
for (urls::const_string seg: u.segments(stack_allocator))   
```

> Username: alandefreitas  
> Created_at: 2022-07-08 16:53:13 UTC  
> Updated_at: 2022-07-08 16:53:27 UTC  
> Url: https://github.com/boostorg/url/pull/226#discussion_r916990709  

Both are useful. Using the stack allocator is useful but it makes no difference in this example because all segments fit in the buffer, while the static allocator might require us to check if the segment fits in the buffer. So I thought we could just make it about `const_string` without the allocator in this example.

> Username: vinniefalco  
> Created_at: 2022-07-08 17:21:33 UTC  
> Url: https://github.com/boostorg/url/pull/226#discussion_r917009210  

It matters to this example, because we are supposed to be exploring the major use-cases. Avoiding allocations is one of those cases. So you should use the stack allocator and deal with overflow.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-07 23:05:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/226#pullrequestreview-1032243186  

📁 example/const_string/query.cpp

```diff
  35 |+ 
  36 |+     return EXIT_SUCCESS;
  37 |+ }
```

> Username: vinniefalco  
> Created_at: 2022-07-07 23:05:03 UTC  
> Url: https://github.com/boostorg/url/pull/226#discussion_r916340023  

missing newline


---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2022-07-08 00:31:08 UTC  
> Updated_at: 2022-07-11 23:42:57 UTC  
> Url: https://github.com/boostorg/url/pull/226#issuecomment-1178402349  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/226?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#226](https://codecov.io/gh/CPPAlliance/url/pull/226?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (9121698) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/ef2db3c1e52474b881812da92ae495cd936ed150?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (ef2db3c) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/226/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/226?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #226   +/-   ##  
========================================  
  Coverage    96.78%   96.78%             
========================================  
  Files          120      120             
  Lines         6068     6068             
========================================  
  Hits          5873     5873             
  Misses         195      195             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/226?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/segments\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/226/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvc2VnbWVudHNfdmlldy5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/226?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/226?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [ef2db3c...9121698](https://codecov.io/gh/CPPAlliance/url/pull/226?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-08-02 04:36:03 UTC  
> Url: https://github.com/boostorg/url/pull/226#issuecomment-1202006853  

We should port the simple route example (not the complex one) into a new single-file example.

---
