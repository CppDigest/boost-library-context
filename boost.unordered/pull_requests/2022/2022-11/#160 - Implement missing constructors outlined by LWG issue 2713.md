# #160 Implement missing constructors outlined by LWG issue 2713 [Merged]

> Username: cmazakas  
> Created at: 2022-11-03 17:25:55 UTC  
> Updated at: 2022-11-05 15:38:20 UTC  
> Merged at: 2022-11-05 12:08:29 UTC  
> Closed at: 2022-11-05 12:08:29 UTC  
> Url: https://github.com/boostorg/unordered/pull/160  

Closes https://github.com/boostorg/unordered/issues/154

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-11-03 19:55:55 UTC  
> Updated_at: 2022-11-05 00:00:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/160#issuecomment-1302597644  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/160?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#160](https://codecov.io/gh/boostorg/unordered/pull/160?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5e76a7b) into [develop](https://codecov.io/gh/boostorg/unordered/commit/71b860d6ae57c2ee7c5ba1f8137eef48e34dea77?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (71b860d) will **increase** coverage by `0.02%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/160/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/160?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #160      +/-   ##  
===========================================  
+ Coverage    97.63%   97.65%   +0.02%       
===========================================  
  Files           83       83                
  Lines        11908    12034     +126       
===========================================  
+ Hits         11626    11752     +126       
  Misses         282      282                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/160?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/unordered\_flat\_map.hpp](https://codecov.io/gh/boostorg/unordered/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX2ZsYXRfbWFwLmhwcA==) | `99.03% <100.00%> (+0.02%)` | :arrow_up: |  
| [include/boost/unordered/unordered\_flat\_set.hpp](https://codecov.io/gh/boostorg/unordered/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX2ZsYXRfc2V0LmhwcA==) | `99.40% <100.00%> (+0.02%)` | :arrow_up: |  
| [include/boost/unordered/unordered\_map.hpp](https://codecov.io/gh/boostorg/unordered/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX21hcC5ocHA=) | `99.85% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/unordered/unordered\_set.hpp](https://codecov.io/gh/boostorg/unordered/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX3NldC5ocHA=) | `99.64% <100.00%> (+0.01%)` | :arrow_up: |  
| [test/unordered/constructor\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/160/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvY29uc3RydWN0b3JfdGVzdHMuY3Bw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/160?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/160?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [71b860d...5e76a7b](https://codecov.io/gh/boostorg/unordered/pull/160?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2022-11-03 22:14:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/160#pullrequestreview-1167731521  

📁 include/boost/unordered/unordered_map.hpp

```diff
1767 |+     unordered_map<K, T, H, P, A>::unordered_map(
1768 |+       InputIterator f, InputIterator l, const allocator_type& a)
1769 |+         : table_(boost::unordered::detail::initial_size(f, l, 0), hasher(),
```

> Username: pdimov  
> Created_at: 2022-11-03 22:14:14 UTC  
> Updated_at: 2022-11-03 22:14:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/160#discussion_r1013443462  

This passes 0 for the initial bucket count...


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2022-11-03 22:14:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/160#pullrequestreview-1167731939  

📁 include/boost/unordered/unordered_map.hpp

```diff
1799 |+       std::initializer_list<value_type> list, const allocator_type& a)
1800 |+         : table_(boost::unordered::detail::initial_size(
1801 |+                    list.begin(), list.end(), detail::default_bucket_count),
```

> Username: pdimov  
> Created_at: 2022-11-03 22:14:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/160#discussion_r1013443943  

... but this passes detail::default_bucket_count. Is this intended?

> Username: pdimov  
> Created_at: 2022-11-03 22:15:38 UTC  
> Url: https://github.com/boostorg/unordered/pull/160#discussion_r1013444701  

The other two places both use detail::default_bucket_count, so probably not.

> Username: cmazakas  
> Created_at: 2022-11-03 22:54:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/160#discussion_r1013469050  

Good catch, we should be using `default_bucket_count` everywhere.


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2022-11-03 22:17:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/160#pullrequestreview-1167734023  

📁 test/unordered/constructor_tests.cpp

```diff
 338 |+     BOOST_ASSERT(vec.size() >= 3);
 339 |+ 
 340 |+     std::initializer_list<value_type> nonempty_list{vec[0], vec[1], vec[2]};
```

> Username: pdimov  
> Created_at: 2022-11-03 22:17:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/160#discussion_r1013446553  

This looks fishy to me. The backing array of the initializer_list is destroyed at the end of the full expression.

> Username: cmazakas  
> Created_at: 2022-11-03 22:26:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/160#discussion_r1013454730  

Hmm, that's a good question.  
  
It seems to pass both sanitizers and checks for the values. For example, we still pass the check where we compare the container's contents to `expected`.

> Username: pdimov  
> Created_at: 2022-11-03 22:56:25 UTC  
> Url: https://github.com/boostorg/unordered/pull/160#discussion_r1013469803  

Yeah, declaring a variable of type initializer_list does extend the lifetime of the temporary backing array, https://eel.is/c++draft/dcl.init.list#6. So it's fine.


---

## Review 5 [Commented]

> Username: pdimov  
> Created_at: 2022-11-03 22:19:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/160#pullrequestreview-1167735550  

📁 test/unordered/constructor_tests.cpp

```diff
 359 |+ 
 360 |+       {
 361 |+         T x(nonempty_list);
```

> Username: pdimov  
> Created_at: 2022-11-03 22:19:52 UTC  
> Url: https://github.com/boostorg/unordered/pull/160#discussion_r1013448499  

This should probably be `T x{ vec[0], vec[1], vec[2] };` instead.

> Username: cmazakas  
> Created_at: 2022-11-03 22:54:18 UTC  
> Url: https://github.com/boostorg/unordered/pull/160#discussion_r1013468750  

This is a nice and simple refactor; will do!


---

## Comment 6

> Username: pdimov  
> Created_at: 2022-11-04 22:35:43 UTC  
> Url: https://github.com/boostorg/unordered/pull/160#issuecomment-1304312780  

This is going into 1.81, right?

---

## Comment 7

> Username: joaquintides  
> Created_at: 2022-11-05 09:09:27 UTC  
> Url: https://github.com/boostorg/unordered/pull/160#issuecomment-1304451689  

> This is going into 1.81, right?  
  
Let's decide, because if it goes in 1.81 I have to update the docs. I'm for it.

---

## Comment 8

> Username: pdimov  
> Created_at: 2022-11-05 11:49:33 UTC  
> Url: https://github.com/boostorg/unordered/pull/160#issuecomment-1304505177  

We still have a few days, but you need to review and merge this to develop before I can merge it to master.

---

## Review 9 [Approved]

> Username: joaquintides  
> Created_at: 2022-11-05 12:08:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/unordered/pull/160#pullrequestreview-1169401017  

Hi Chris, I think the `boost::unordered::detail::default_bucket_count ` construct should be mimicked in foa with a dedicated, separate `boost::unordered::detail::foa::default_bucket_count`. As this is not directly related to the PR at hand, I'm approving but please keep this in your backlog. Thank you!

---
