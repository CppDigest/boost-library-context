# #277 Fixed `std::initializer_list` assignment issues for open-addressing containers [Merged]

> Username: joaquintides  
> Created at: 2024-09-01 15:23:57 UTC  
> Updated at: 2024-09-02 16:56:16 UTC  
> Merged at: 2024-09-02 16:56:13 UTC  
> Closed at: 2024-09-02 16:56:13 UTC  
> Url: https://github.com/boostorg/unordered/pull/277  

Fixes #276.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-09-01 15:32:41 UTC  
> Url: https://github.com/boostorg/unordered/pull/277#issuecomment-2323397415  

An automated preview of the documentation is available at [https://277.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://277.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 2 [Commented]

> Username: k3DW  
> Created_at: 2024-09-01 16:31:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/277#pullrequestreview-2274453133  

📁 include/boost/unordered/unordered_flat_map.hpp

```diff
 207 |+       {
 208 |+         clear();
 209 |+         insert(il);
```

> Username: k3DW  
> Created_at: 2024-09-01 16:31:23 UTC  
> Updated_at: 2024-09-01 16:31:58 UTC  
> Url: https://github.com/boostorg/unordered/pull/277#discussion_r1740158369  

You could save a function call by instead writing  
```cpp  
insert(il.begin(), il.end());  
```

> Username: joaquintides  
> Created_at: 2024-09-02 07:54:05 UTC  
> Updated_at: 2024-09-02 07:54:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/277#discussion_r1740478695  

Done, thank you!


---

## Comment 3

> Username: pdimov  
> Created_at: 2024-09-01 19:10:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/277#issuecomment-2323464182  

That's fine in principle but I prefer writing tests that reflect actual use. Nobody's going to assign `std::initializer_list<value_type>()` to a container; more probable user code would be to assign something like `{{k1, v1}, {k2, v2}}`.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2024-09-01 22:20:13 UTC  
> Url: https://github.com/boostorg/unordered/pull/277#issuecomment-2323517336  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/277?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `79.41176%` with `7 lines` in your changes missing coverage. Please review.  
> Project coverage is 98.13%. Comparing base [(`2193724`)](https://app.codecov.io/gh/boostorg/unordered/commit/21937249c47da1fbdb445dc40775a88832fd772d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`4d22231`)](https://app.codecov.io/gh/boostorg/unordered/commit/4d22231273939732da9d01ab3db1fb961888562d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/unordered/pull/277?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [test/objects/non\_default\_ctble\_allocator.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/277?src=pr&el=tree&filepath=test%2Fobjects%2Fnon_default_ctble_allocator.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9vYmplY3RzL25vbl9kZWZhdWx0X2N0YmxlX2FsbG9jYXRvci5ocHA=) | 22.22% | [7 Missing :warning: ](https://app.codecov.io/gh/boostorg/unordered/pull/277?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/277/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/277?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #277      +/-   ##  
===========================================  
- Coverage    98.16%   98.13%   -0.03%       
===========================================  
  Files          159      160       +1       
  Lines        21486    21520      +34       
===========================================  
+ Hits         21092    21119      +27       
- Misses         394      401       +7       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/unordered/pull/277?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/unordered\_flat\_map.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/277?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Funordered_flat_map.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX2ZsYXRfbWFwLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/unordered/unordered\_flat\_set.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/277?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Funordered_flat_set.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX2ZsYXRfc2V0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/unordered/unordered\_node\_map.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/277?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Funordered_node_map.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX25vZGVfbWFwLmhwcA==) | `99.24% <100.00%> (+0.01%)` | :arrow_up: |  
| [include/boost/unordered/unordered\_node\_set.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/277?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Funordered_node_set.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX25vZGVfc2V0LmhwcA==) | `99.03% <100.00%> (+0.01%)` | :arrow_up: |  
| [test/cfoa/assign\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/277?src=pr&el=tree&filepath=test%2Fcfoa%2Fassign_tests.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL2Fzc2lnbl90ZXN0cy5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/unordered/assign\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/277?src=pr&el=tree&filepath=test%2Funordered%2Fassign_tests.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvYXNzaWduX3Rlc3RzLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/unordered/node\_handle\_allocator\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/277?src=pr&el=tree&filepath=test%2Funordered%2Fnode_handle_allocator_tests.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvbm9kZV9oYW5kbGVfYWxsb2NhdG9yX3Rlc3RzLmNwcA==) | `100.00% <ø> (ø)` | |  
| [test/objects/non\_default\_ctble\_allocator.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/277?src=pr&el=tree&filepath=test%2Fobjects%2Fnon_default_ctble_allocator.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9vYmplY3RzL25vbl9kZWZhdWx0X2N0YmxlX2FsbG9jYXRvci5ocHA=) | `22.22% <22.22%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/277?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/277?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2193724...4d22231](https://app.codecov.io/gh/boostorg/unordered/pull/277?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-09-02 08:02:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/277#issuecomment-2324075351  

An automated preview of the documentation is available at [https://277.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://277.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 6

> Username: joaquintides  
> Created_at: 2024-09-02 08:22:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/277#issuecomment-2324116662  

> That's fine in principle but I prefer writing tests that reflect actual use. Nobody's going to assign std::initializer_list<value_type>() to a container; more probable user code would be to assign something like {{k1, v1}, {k2, v2}}.  
  
Fixed [here](https://github.com/boostorg/unordered/pull/277/commits/cc8b40345ef78ea191c4e6976e0f8d0b4365131f).

---
