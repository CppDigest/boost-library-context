# #177 Feature/p2363 [Merged]

> Username: cmazakas  
> Created at: 2022-12-14 21:41:30 UTC  
> Updated at: 2022-12-20 18:55:23 UTC  
> Merged at: 2022-12-20 18:55:20 UTC  
> Closed at: 2022-12-20 18:55:20 UTC  
> Url: https://github.com/boostorg/unordered/pull/177  

Implement the rest of the transparent operations as specified by: http://wg21.link/P2363  
  
Addresses https://github.com/boostorg/unordered/issues/150

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-12-15 01:41:08 UTC  
> Updated_at: 2022-12-20 15:47:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#issuecomment-1352447320  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/177?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#177](https://codecov.io/gh/boostorg/unordered/pull/177?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1955e4f) into [develop](https://codecov.io/gh/boostorg/unordered/commit/eb33ad3e3f416ae94dfa216b06509ebb4b9a67eb?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (eb33ad3) will **increase** coverage by `0.10%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 1955e4f differs from pull request most recent head 955f943. Consider uploading reports for the commit 955f943 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/177/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #177      +/-   ##  
===========================================  
+ Coverage    97.62%   97.72%   +0.10%       
===========================================  
  Files           83       85       +2       
  Lines        12263    12720     +457       
===========================================  
+ Hits         11972    12431     +459       
+ Misses         291      289       -2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/177?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/implementation.hpp](https://codecov.io/gh/boostorg/unordered/pull/177/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ltcGxlbWVudGF0aW9uLmhwcA==) | `99.25% <100.00%> (+0.08%)` | :arrow_up: |  
| [include/boost/unordered/unordered\_flat\_map.hpp](https://codecov.io/gh/boostorg/unordered/pull/177/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX2ZsYXRfbWFwLmhwcA==) | `99.55% <100.00%> (+0.54%)` | :arrow_up: |  
| [include/boost/unordered/unordered\_flat\_set.hpp](https://codecov.io/gh/boostorg/unordered/pull/177/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX2ZsYXRfc2V0LmhwcA==) | `99.40% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/unordered/unordered\_map.hpp](https://codecov.io/gh/boostorg/unordered/pull/177/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX21hcC5ocHA=) | `99.85% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/unordered/unordered\_set.hpp](https://codecov.io/gh/boostorg/unordered/pull/177/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX3NldC5ocHA=) | `99.65% <100.00%> (+<0.01%)` | :arrow_up: |  
| [test/unordered/transparent\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/177/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvdHJhbnNwYXJlbnRfdGVzdHMuY3Bw) | `94.00% <100.00%> (+2.09%)` | :arrow_up: |  
| [include/boost/unordered/detail/prime\_fmod.hpp](https://codecov.io/gh/boostorg/unordered/pull/177/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL3ByaW1lX2Ztb2QuaHBw) | `100.00% <0.00%> (ø)` | |  
| [test/unordered/narrow\_cast\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/177/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvbmFycm93X2Nhc3RfdGVzdHMuY3Bw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/unordered/detail/narrow\_cast.hpp](https://codecov.io/gh/boostorg/unordered/pull/177/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL25hcnJvd19jYXN0LmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/unordered/detail/foa.hpp](https://codecov.io/gh/boostorg/unordered/pull/177/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS5ocHA=) | `98.63% <0.00%> (+<0.01%)` | :arrow_up: |  
| ... and [1 more](https://codecov.io/gh/boostorg/unordered/pull/177/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/177?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/177?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [eb33ad3...955f943](https://codecov.io/gh/boostorg/unordered/pull/177?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Changes requested]

> Username: joaquintides  
> Created_at: 2022-12-16 13:01:36 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/unordered/pull/177#pullrequestreview-1220759608  

* Additionally to the in-code comments, the PR lacks documentation. I can do this over the weekend.

📁 include/boost/unordered/detail/implementation.hpp

```diff
1504 |+         {
1505 |+           return construct_node_pair(alloc, boost::forward<Key>(k));
1506 |+         }
```

> Username: joaquintides  
> Created_at: 2022-12-16 12:11:37 UTC  
> Updated_at: 2022-12-16 13:01:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#discussion_r1050693456  

I understand this new `try_construct_node` memfun is introduced to make `try_emplace_unique` usable from `unordered_[multi]set`,  please confirm. If so:  
* Why is the first overload not delegating to `construct_node` directly?  
* I find the `try_` prefix a little misleading. Maybe `construct_node_or_node_pair`?

> Username: cmazakas  
> Created_at: 2022-12-16 22:17:53 UTC  
> Updated_at: 2022-12-16 22:17:54 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#discussion_r1051204496  

Yeah, the `try_` prefix came from the fact that these functions are _only_ being used in `try_emplace_unique`.  
  
Good call on the first overload not delegating properly as it should.  
  
`construct_node_or_node_pair` is fine. Maybe something like `construct_node_from_key_only` could also work.


📁 include/boost/unordered/detail/type_traits.hpp

```diff
  58 |         static bool const value =
  59 |-           is_transparent<A>::value && is_transparent<B>::value;
  59 |+           is_transparent<Hash>::value && is_transparent<KeyEqual>::value;
```

> Username: joaquintides  
> Created_at: 2022-12-16 12:22:57 UTC  
> Updated_at: 2022-12-16 13:01:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#discussion_r1050707898  

Not related to this PR, but why does `are_transparent` take an unused template param?

> Username: cmazakas  
> Created_at: 2022-12-16 21:05:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#discussion_r1051160602  

The SFINAE for the transparent member functions only works when they're dependent on the template parameter so we use a dummy here to keep the check dependent.


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-12-16 23:55:56 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#issuecomment-1355845217  

An automated preview of the documentation is available at [https://177.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://177.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 4

> Username: joaquintides  
> Created_at: 2022-12-18 11:39:47 UTC  
> Updated_at: 2022-12-19 12:05:39 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#issuecomment-1356779864  

While updating the docs, I noticed that `unordered_map::try_emplace` (unlike `emplace`) does not add overloads up to 10 arguments to emulate variadic args, despite what the reference says. Not related to PR, you may want to file an issue.

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-12-18 11:40:56 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#issuecomment-1356780068  

An automated preview of the documentation is available at [https://177.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://177.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 6 [Changes requested]

> Username: joaquintides  
> Created_at: 2022-12-19 08:38:20 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/unordered/pull/177#pullrequestreview-1222489258  

There's a bug with the P2363 spec that shows in our implementation of it:  
```cpp  
struct transparent_hash  
{  
  using is_transparent=void;  
  
  template<typename T>  
  std::size_t operator()(const T& x)const  
  {  
    return boost::hash<T>()(x);  
  }  
};  
  
using transparent_set=boost::unordered_flat_set<int,transparent_hash,std::equal_to<>>;  
...  
transparent_set ts1,ts2;  
ts1.insert(ts2.begin(),ts2.end());  
```  
The call to `insert` is ambiguous (could be heterogeneous-lookup `insert` with a hint or range `insert`).

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-12-19 20:56:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#issuecomment-1358337061  

An automated preview of the documentation is available at [https://177.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://177.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-12-19 21:31:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#issuecomment-1358400051  

An automated preview of the documentation is available at [https://177.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://177.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-12-20 10:46:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#issuecomment-1359166313  

An automated preview of the documentation is available at [https://177.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://177.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 10 [Commented]

> Username: pdimov  
> Created_at: 2022-12-20 14:57:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/177#pullrequestreview-1224634390  

📁 doc/unordered/unordered_flat_map.adoc

```diff
 989 | Throws:;; If an exception is thrown by an operation other than a call to `hasher` the function has no effect.
 951 |- Notes:;; Can invalidate iterators pointers and references, but only if the insert causes the load to be greater than the maximum load. +
 990 |+ Notes:;; Can invalidate iterators pointers and references, but only if the insert causes the load to be greater than the maximum load.  +
```

> Username: pdimov  
> Created_at: 2022-12-20 14:57:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#discussion_r1053409968  

Is `:;;` correct here? Shouldn't it be `:::`?

> Username: joaquintides  
> Created_at: 2022-12-20 15:40:38 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#discussion_r1053458277  

This is what I found when I begin updating the docs (no ocurrence of `:::` overall), and it displays OK, but other than this I don't know. Maybe @cmazakas knows more about Asciidoc syntax to shed some light here.

> Username: pdimov  
> Created_at: 2022-12-20 15:56:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#discussion_r1053474752  

Maybe `;;` is an alternative token?

> Username: pdimov  
> Created_at: 2022-12-20 15:58:30 UTC  
> Updated_at: 2022-12-20 15:58:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#discussion_r1053477542  

Yes, it is. https://docs.asciidoctor.org/asciidoc/latest/lists/description/


---

## Review 11 [Commented]

> Username: pdimov  
> Created_at: 2022-12-20 14:57:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/177#pullrequestreview-1224635659  

📁 doc/unordered/unordered_multimap.adoc

```diff
1306 | ```c++
1307 | size_type bucket(const key_type& k) const;
1308 |+ templat<class K> size_type bucket(const K& k) const;
```

> Username: pdimov  
> Created_at: 2022-12-20 14:57:58 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#discussion_r1053410842  

typo: `templat`

> Username: joaquintides  
> Created_at: 2022-12-20 15:50:55 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#discussion_r1053469212  

Fixed


---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-12-20 15:56:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/177#issuecomment-1359604405  

An automated preview of the documentation is available at [https://177.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://177.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
