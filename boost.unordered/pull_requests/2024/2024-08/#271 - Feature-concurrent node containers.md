# #271 Feature/concurrent node containers [Merged]

> Username: joaquintides  
> Created at: 2024-08-05 11:21:42 UTC  
> Updated at: 2024-08-25 16:35:08 UTC  
> Merged at: 2024-08-25 16:34:58 UTC  
> Closed at: 2024-08-25 16:34:58 UTC  
> Url: https://github.com/boostorg/unordered/pull/271  

Adds `boost::concurrent_node_(map|set)`. The following is _not_ part of this PR and will be done later:  
  
* Upadate concurrent benchmarks to include the new node-based containers.  
* Include `(insert|emplace)_and_visit` operations. This is still under discussion (conversation on the #boost-unordered Slack group).

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-08-05 11:32:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2268855150  

An automated preview of the documentation is available at [https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-08-05 11:40:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2268868853  

An automated preview of the documentation is available at [https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 3 [Commented]

> Username: cmazakas  
> Created_at: 2024-08-05 15:21:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/271#pullrequestreview-2219284672  

📁 include/boost/unordered/detail/foa/core.hpp

```diff
1461 |+ #if defined(BOOST_GCC)
1462 |+ #pragma GCC diagnostic push
1463 |+ #pragma GCC diagnostic ignored "-Wmaybe-uninitialized"
```

> Username: cmazakas  
> Created_at: 2024-08-05 15:21:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#discussion_r1704286321  

A `maybe-uninitialized` warning? What did the warning say, out of curiosity?  
  
Historically, this might mean we need a launder() call somewhere.

> Username: joaquintides  
> Created_at: 2024-08-06 14:08:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#discussion_r1705609153  

It is with this type of constructor:  
  
```cpp  
  table_core(  
    std::size_t n=default_bucket_count,const Hash& h_=Hash(),  
    const Pred& pred_=Pred(),const Allocator& al_=Allocator()):  
    hash_base{empty_init,h_},pred_base{empty_init,pred_},  
    allocator_base{empty_init,al_},arrays(new_arrays(n)),  
    size_ctrl{initial_max_load(),0}  
    {}  
```  
As it happens, `arrays` (in `arrays(new_arrays(n))`) is a non-static member function that uses the internal allocator, but _not_ other data members that have not been initialized yet (namely `size_ctrl`), but the compiler's analysis is not as fine-grained as that, seemingly. The situation would be similar to this example:  
  
```cpp  
struct X  
{  
  // calculate_b() called before X is fully initialized, but it's OK as b and c do not  
  // form part of the calculation.  
    
  X(int a_): a(a_), b(calculate_b()), c(0){}  
  
  int calculate_b() { return 2*a; }  
    
  int a;  
  int b;  
  int c;  
};  
```  
What I find surprising is that this warning didn't trigger before, maybe it only happens for complex, non-inlined scenarios.


---

## Review 4 [Commented]

> Username: k3DW  
> Created_at: 2024-08-05 21:30:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/271#pullrequestreview-2219895122  

📁 extra/boost_unordered.natvis

```diff
 422 |         <AlternativeType Name="boost::unordered::concurrent_flat_map&lt;*&gt;" />
 423 |         <AlternativeType Name="boost::unordered::concurrent_flat_set&lt;*&gt;" />
 424 |+         <AlternativeType Name="boost::unordered::concurrent_node_map&lt;*&gt;" />
```

> Username: k3DW  
> Created_at: 2024-08-05 21:30:42 UTC  
> Updated_at: 2024-08-05 21:30:43 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#discussion_r1704650979  

Another copy of this line needs to be inserted to 438 below, otherwise the `concurrent_node_map` will be displayed like a set. (i.e. using the indices in the item name as opposed to using the keys)

> Username: joaquintides  
> Created_at: 2024-08-06 09:07:24 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#discussion_r1705186194  

Thank you! Fixed.


---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2024-08-06 01:41:48 UTC  
> Updated_at: 2024-08-06 07:23:10 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2270202802  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/271?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `97.42268%` with `20 lines` in your changes missing coverage. Please review.  
> Project coverage is 98.02%. Comparing base [(`1d1f0d3`)](https://app.codecov.io/gh/boostorg/unordered/commit/1d1f0d306d2220d24f673f207a3b6cc7c8663e27?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`c191c78`)](https://app.codecov.io/gh/boostorg/unordered/commit/c191c78d3174b27c73101c0ee1e84f6b68c50c55?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
| [Files](https://app.codecov.io/gh/boostorg/unordered/pull/271?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/unordered/detail/foa/node\_handle.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffoa%2Fnode_handle.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9ub2RlX2hhbmRsZS5ocHA=) | 58.33% | [10 Missing :warning: ](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/unordered/concurrent\_node\_map.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fconcurrent_node_map.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvY29uY3VycmVudF9ub2RlX21hcC5ocHA=) | 98.08% | [5 Missing :warning: ](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
| [include/boost/unordered/concurrent\_node\_set.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fconcurrent_node_set.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvY29uY3VycmVudF9ub2RlX3NldC5ocHA=) | 97.88% | [5 Missing :warning: ](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/271/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #271      +/-   ##  
===========================================  
- Coverage    98.07%   98.02%   -0.05%       
===========================================  
  Files          152      157       +5       
  Lines        20636    21369     +733       
===========================================  
+ Hits         20238    20948     +710       
- Misses         398      421      +23       
```  
  
| [Files](https://app.codecov.io/gh/boostorg/unordered/pull/271?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...de/boost/unordered/detail/foa/concurrent\_table.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffoa%2Fconcurrent_table.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb25jdXJyZW50X3RhYmxlLmhwcA==) | `99.43% <100.00%> (-0.12%)` | :arrow_down: |  
| [include/boost/unordered/detail/foa/core.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffoa%2Fcore.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb3JlLmhwcA==) | `99.54% <ø> (+<0.01%)` | :arrow_up: |  
| [...ude/boost/unordered/detail/foa/node\_map\_handle.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffoa%2Fnode_map_handle.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9ub2RlX21hcF9oYW5kbGUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [...ude/boost/unordered/detail/foa/node\_set\_handle.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Fdetail%2Ffoa%2Fnode_set_handle.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9ub2RlX3NldF9oYW5kbGUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/unordered/unordered\_node\_map.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Funordered_node_map.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX25vZGVfbWFwLmhwcA==) | `99.23% <100.00%> (-0.01%)` | :arrow_down: |  
| [include/boost/unordered/unordered\_node\_set.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&filepath=include%2Fboost%2Funordered%2Funordered_node_set.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX25vZGVfc2V0LmhwcA==) | `99.01% <100.00%> (+<0.01%)` | :arrow_up: |  
| [test/cfoa/assign\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&filepath=test%2Fcfoa%2Fassign_tests.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL2Fzc2lnbl90ZXN0cy5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/cfoa/clear\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&filepath=test%2Fcfoa%2Fclear_tests.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL2NsZWFyX3Rlc3RzLmNwcA==) | `98.27% <ø> (ø)` | |  
| [test/cfoa/common\_helpers.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&filepath=test%2Fcfoa%2Fcommon_helpers.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL2NvbW1vbl9oZWxwZXJzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [test/cfoa/constructor\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree&filepath=test%2Fcfoa%2Fconstructor_tests.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL2NvbnN0cnVjdG9yX3Rlc3RzLmNwcA==) | `99.43% <100.00%> (+<0.01%)` | :arrow_up: |  
| ... and [25 more](https://app.codecov.io/gh/boostorg/unordered/pull/271?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [6 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/unordered/pull/271/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/271?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/271?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1d1f0d3...c191c78](https://app.codecov.io/gh/boostorg/unordered/pull/271?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-08-06 09:17:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2270797496  

An automated preview of the documentation is available at [https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-08-06 15:47:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2271610268  

An automated preview of the documentation is available at [https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2024-08-07 11:57:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2273298105  

An automated preview of the documentation is available at [https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2024-08-07 16:22:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2273852550  

An automated preview of the documentation is available at [https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2024-08-07 17:32:51 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2273979950  

An automated preview of the documentation is available at [https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2024-08-07 18:37:52 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2274101429  

An automated preview of the documentation is available at [https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2024-08-08 11:12:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2275560859  

An automated preview of the documentation is available at [https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2024-08-08 11:42:52 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2275614848  

An automated preview of the documentation is available at [https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2024-08-09 08:22:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2277422349  

An automated preview of the documentation is available at [https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2024-08-09 10:32:52 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2277650587  

An automated preview of the documentation is available at [https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 16 [Commented]

> Username: pdimov  
> Created_at: 2024-08-18 07:53:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/271#pullrequestreview-2244247308  

📁 doc/unordered/unordered_node_map.adoc

```diff
 542 |+ 
 543 |+ ```c++
 544 |+ unordered_flat_map(concurrent_node_map<Key, T, Hash, Pred, Allocator>&& other);
```

> Username: pdimov  
> Created_at: 2024-08-18 07:53:34 UTC  
> Updated_at: 2024-08-18 07:53:35 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#discussion_r1720917231  

This should presumably say `unordered_node_map`?

> Username: joaquintides  
> Created_at: 2024-08-18 17:13:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#discussion_r1721022824  

Good catch, fixed.


---

## Review 17 [Commented]

> Username: pdimov  
> Created_at: 2024-08-18 07:57:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/271#pullrequestreview-2244247824  

📁 include/boost/unordered/detail/foa/node_handle.hpp

```diff
 189 |+        * allocator object under some circumstances.
 190 |+        */
 191 |+       if(empty())__builtin_unreachable();
```

> Username: pdimov  
> Created_at: 2024-08-18 07:57:01 UTC  
> Updated_at: 2024-08-18 07:57:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#discussion_r1720917906  

This seems fishy to me. Is calling `get_allocator` undefined behavior when `empty()`? Is the removal of the `noexcept` on purpose?

> Username: joaquintides  
> Created_at: 2024-08-18 17:31:48 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#discussion_r1721025709  

* `get_allocator` on an empty node handle is UB, see [here](https://eel.is/c++draft/container.node#observers-11).  
* I removed `noexcept` on purpose cause the standard does not have it (presumably because of the Lakos rule on narrow-contract functions). This removal is not materially connected to the `__builtin_unreachable` addition, really.  
* The `__builtin_unreachable` addition is due to GCC incorrectly, and not systematically, warning about potentially uninitialized node handles [here](https://github.com/boostorg/unordered/blob/bd5eae3c1a97d5557e8a0b4bfc513d1eed3c14bd/test/unordered/node_handle_allocator_tests.cpp#L130) and [here](https://github.com/boostorg/unordered/blob/bd5eae3c1a97d5557e8a0b4bfc513d1eed3c14bd/test/unordered/node_handle_allocator_tests.cpp#L138): adding  `__builtin_unreachable` helped _but not always_, so I was forced to also add [this](https://github.com/boostorg/unordered/blob/bd5eae3c1a97d5557e8a0b4bfc513d1eed3c14bd/test/unordered/node_handle_allocator_tests.cpp#L7-L13). I don't see any problem with foolproofing `node_handle_base::get_allocator` as I've done, but if it looks smelly/ugly I can just remove it, of course --the GCC pragma in `node_handle_allocator_tests.cpp` has us covered.


---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2024-08-18 17:22:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2295332239  

An automated preview of the documentation is available at [https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 19

> Username: k3DW  
> Created_at: 2024-08-24 22:12:27 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2308555200  

I caused the merge conflict with my other PR, so @joaquintides gave me permission to resolve it here.

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2024-08-24 22:22:48 UTC  
> Url: https://github.com/boostorg/unordered/pull/271#issuecomment-2308557056  

An automated preview of the documentation is available at [https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://271.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
