# #895 Tidy-up noexcept move operations in basic_fields [Closed]

> Username: djarek  
> Created at: 2017-11-18 19:17:04 UTC  
> Updated at: 2019-09-10 21:02:36 UTC  
> Closed at: 2017-11-29 04:10:24 UTC  
> Url: https://github.com/boostorg/beast/pull/895  

Adds failing tests for #894.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-11-27 00:08:06 UTC  
> Updated_at: 2017-11-28 15:35:00 UTC  
> Url: https://github.com/boostorg/beast/pull/895#issuecomment-347050346  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/895?src=pr&el=h1) Report  
> Merging [#895](https://codecov.io/gh/boostorg/beast/pull/895?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/5d83c79be408c8a29dd7eec413e9dc699648cdba?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/895/graphs/tree.svg?height=150&width=650&token=Gq6MFA9JRF&src=pr)](https://codecov.io/gh/boostorg/beast/pull/895?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #895      +/-   ##  
===========================================  
+ Coverage    95.68%   95.68%   +<.01%       
===========================================  
  Files          104      104                
  Lines        10441    10454      +13       
===========================================  
+ Hits          9990    10003      +13       
  Misses         451      451  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/895?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/895?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/http/impl/fields.ipp](https://codecov.io/gh/boostorg/beast/pull/895?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `97.72% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/string.hpp](https://codecov.io/gh/boostorg/beast/pull/895?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3N0cmluZy5ocHA=) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/detail/type\_traits.hpp](https://codecov.io/gh/boostorg/beast/pull/895?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC90eXBlX3RyYWl0cy5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/895?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/895?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/895?src=pr&el=footer). Last update [5d83c79...6998329](https://codecov.io/gh/boostorg/beast/pull/895?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-11-27 21:46:42 UTC  
> Url: https://github.com/boostorg/beast/pull/895#issuecomment-347338933  

Please squash this down into one commit, suggested message "Tidy up basic_fields exception specifiers"

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-11-27 21:47:13 UTC  
> Url: https://github.com/boostorg/beast/pull/895#issuecomment-347339074  

@glenfe how do you feel about these changes?

---

## Review 4 [Commented]

> Username: glenfe  
> Created_at: 2017-11-28 00:50:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/895#pullrequestreview-79361559  

📁 include/boost/beast/http/fields.hpp

```diff
 248 |-     basic_fields& operator=(basic_fields&&);
 261 |+     basic_fields& operator=(basic_fields&&) noexcept(
 262 |+         alloc_traits::propagate_on_container_move_assignment::value);
```

> Username: glenfe  
> Created_at: 2017-11-28 00:48:28 UTC  
> Updated_at: 2017-11-28 07:51:10 UTC  
> Url: https://github.com/boostorg/beast/pull/895#discussion_r153367053  

Should also require `is_nothrow_move_assignable<Allocator>::value` (in addition to POCMA) in the pre-C++17 world. In the post-C++17 world, should require `alloc_traits::is_always_equal`.

> Username: djarek  
> Created_at: 2017-11-28 01:45:13 UTC  
> Updated_at: 2017-11-28 07:51:10 UTC  
> Url: https://github.com/boostorg/beast/pull/895#discussion_r153375122  

This is part of the requirements of the Allocator concept. Should we check them with static_asserts?

> Username: glenfe  
> Created_at: 2017-11-28 01:57:27 UTC  
> Updated_at: 2017-11-28 07:51:10 UTC  
> Url: https://github.com/boostorg/beast/pull/895#discussion_r153376737  

For the latter, if in C++17 or higher mode, it should be in the noexcept condition. i.e. `noexcept(alloc_traits::propagate_on_container_move_assignment::value || alloc_traits::is_always_equal::value)`.  
  
Before C++17 the Allocator requirements were a little more relaxed. You could use `noexcept(alloc_traits::propagate_on_container_move_assignment::value && is_nothrow_move_assignable<Allocator>::value)` if in C++11/14 mode, but in practice I think you're fine with what you have (and just `static_assert`).


---

## Review 5 [Approved]

> Username: glenfe  
> Created_at: 2017-11-28 00:53:16 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/895#pullrequestreview-79362285  

All in all, looks good to me.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2017-11-28 02:09:17 UTC  
> Updated_at: 2017-11-28 02:10:41 UTC  
> Url: https://github.com/boostorg/beast/pull/895#issuecomment-347390709  

Commit message should read:  
```  
Tidy up basic_fields exception specifiers  
  
fix #894  
  
Signed-off-by: Damian Jarek <damian.jarek93@gmail.com>  
```  
  
Note the blank lines. Also, please add a line to CHANGELOG.md (rebase against my **147** branch first: https://github.com/vinniefalco/beast/tree/v147)  
  
Thanks!

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-11-29 04:10:24 UTC  
> Url: https://github.com/boostorg/beast/pull/895#issuecomment-347747344  

Merged to **147**, thanks!

---
