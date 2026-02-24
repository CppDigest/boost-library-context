# #230 Optimize `emplace()` for arguments of the form `k, v` [Merged]

> Username: k3DW  
> Created at: 2024-01-20 05:25:48 UTC  
> Updated at: 2024-02-29 01:52:15 UTC  
> Merged at: 2024-02-12 16:24:49 UTC  
> Closed at: 2024-02-12 16:24:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/230  

Closes #226

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-01-20 05:38:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#issuecomment-1901768452  

An automated preview of the documentation is available at [https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-01-20 06:43:23 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#issuecomment-1901790990  

An automated preview of the documentation is available at [https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 3 [Commented]

> Username: cmazakas  
> Created_at: 2024-01-22 16:03:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/230#pullrequestreview-1836823075  

📁 include/boost/unordered/detail/implementation.hpp

```diff
1925 |+             typename detail::rebind_wrap<node_allocator_type, key_type>::type;
1926 |+ 
1927 |+           alignas(key_type) unsigned char storage[sizeof(key_type)];
```

> Username: cmazakas  
> Created_at: 2024-01-22 16:03:13 UTC  
> Updated_at: 2024-01-22 16:03:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1462078337  

We already have this pattern in the codebase, check out https://github.com/boostorg/unordered/blob/67c5cdb3a69f0b92d2779880ce9aa1d46e54cf7b/include/boost/unordered/detail/opt_storage.hpp

> Username: k3DW  
> Created_at: 2024-01-22 17:34:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1462193146  

I tried using `opt_storage` here and it's fine, but it gives a compile error because of deleted copy constructor in the `detail::foa::allocator_constructed` class created for this exact same purpose. I think instead I'll factor these 2 classes into a single implementation


---

## Review 4 [Commented]

> Username: cmazakas  
> Created_at: 2024-01-22 16:03:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/230#pullrequestreview-1836824616  

📁 include/boost/unordered/detail/implementation.hpp

```diff
1923 |+           using key_type = typename Types::key_type;
1924 |+           using key_allocator_type =
1925 |+             typename detail::rebind_wrap<node_allocator_type, key_type>::type;
```

> Username: cmazakas  
> Created_at: 2024-01-22 16:03:55 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1462079236  

`construct()` is also a template so you don't need to rebind here


---

## Review 5 [Commented]

> Username: cmazakas  
> Created_at: 2024-01-22 16:10:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/230#pullrequestreview-1836838866  

📁 doc/unordered/changes.adoc

```diff
   9 |+ == Release 1.85.0
  10 |+ 
  11 |+ * Optimized `emplace()` for a `value_type` or `init_type` (if applicable) argument to only allocate once.
```

> Username: cmazakas  
> Created_at: 2024-01-22 16:10:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1462087860  

The use of "allocate once" is confusing to me here. What's meant by it?

> Username: k3DW  
> Created_at: 2024-01-24 19:08:37 UTC  
> Updated_at: 2024-01-24 19:09:54 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1465421360  

I'll make it more clear. When we're given a `value_type` or `init_type` as an argument to `emplace()`, we can bypass creating an intermediate object, which we would otherwise create.


---

## Review 6 [Commented]

> Username: cmazakas  
> Created_at: 2024-01-22 16:25:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/230#pullrequestreview-1836869357  

📁 include/boost/unordered/detail/foa/core.hpp

```diff
1419 |+     is_map::value && (detail::is_similar<K, key_type>::value ||
1420 |+                        is_key_type_move_constructible::value)>;
1421 |+ 
```

> Username: cmazakas  
> Created_at: 2024-01-22 16:25:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1462106857  

Is there a way to keep these traits from having to be protected member typedefs?

> Username: k3DW  
> Created_at: 2024-01-24 19:11:32 UTC  
> Updated_at: 2024-01-24 19:11:33 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1465424212  

They can be free traits instead. I'm not sure about the naming though. I'll take any suggestions


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-01-24 20:03:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#issuecomment-1908830984  

An automated preview of the documentation is available at [https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2024-02-07 06:08:24 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#issuecomment-1931353964  

An automated preview of the documentation is available at [https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 9

> Username: codecov[bot]  
> Created_at: 2024-02-07 09:25:22 UTC  
> Updated_at: 2024-02-11 20:26:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#issuecomment-1931620440  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: `1 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`1df3705`)](https://app.codecov.io/gh/boostorg/unordered/commit/1df37051ae8c433d42342eb7fbd071fa2b847652?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 98.04% compared to head [(`bbad473`)](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 98.06%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/230/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #230      +/-   ##  
===========================================  
+ Coverage    98.04%   98.06%   +0.02%       
===========================================  
  Files          145      146       +1       
  Lines        19853    20038     +185       
===========================================  
+ Hits         19464    19651     +187       
+ Misses         389      387       -2       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...e/boost/unordered/detail/allocator\_constructed.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2FsbG9jYXRvcl9jb25zdHJ1Y3RlZC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [...de/boost/unordered/detail/foa/concurrent\_table.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb25jdXJyZW50X3RhYmxlLmhwcA==) | `99.69% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/unordered/detail/foa/core.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb3JlLmhwcA==) | `99.84% <100.00%> (+<0.01%)` | :arrow_up: |  
| [...lude/boost/unordered/detail/foa/flat\_map\_types.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9mbGF0X21hcF90eXBlcy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [...lude/boost/unordered/detail/foa/node\_map\_types.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9ub2RlX21hcF90eXBlcy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/unordered/detail/foa/table.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS90YWJsZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/unordered/detail/implementation.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ltcGxlbWVudGF0aW9uLmhwcA==) | `99.14% <100.00%> (+<0.01%)` | :arrow_up: |  
| [test/cfoa/emplace\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL2VtcGxhY2VfdGVzdHMuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/helpers/count.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9oZWxwZXJzL2NvdW50LmhwcA==) | `85.48% <100.00%> (+3.51%)` | :arrow_up: |  
| [test/unordered/emplace\_smf\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvZW1wbGFjZV9zbWZfdGVzdHMuY3Bw) | `100.00% <100.00%> (ø)` | |  
| ... and [2 more](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1df3705...bbad473](https://app.codecov.io/gh/boostorg/unordered/pull/230?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2024-02-07 21:43:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#issuecomment-1932987537  

An automated preview of the documentation is available at [https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 11 [Commented]

> Username: joaquintides  
> Created_at: 2024-02-08 18:26:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/230#pullrequestreview-1870648922  

📁 doc/unordered/changes.adoc

```diff
  11 |+ * Optimized `emplace()` for a `value_type` or `init_type` (if applicable) argument to bypass creating an intermediate object. The argument is already the same type as the would-be intermediate object.
  12 |+ * Optimized `emplace()` for `k,v` arguments on map containers to delay constructing the object until it is certain that an element should be inserted. This optimization when the map's `key_type` is move constructible or when the `k` argument is a `key_type`.
  13 |+ 
```

> Username: joaquintides  
> Created_at: 2024-02-08 16:06:06 UTC  
> Updated_at: 2024-02-08 18:27:46 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1483226528  

The statement "this optimization when..." lacks a verb in its main clause.

> Username: k3DW  
> Created_at: 2024-02-10 17:11:37 UTC  
> Updated_at: 2024-02-10 17:51:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1485203542  

I missed the word "happens", thanks


📁 test/unordered/init_type_insert_tests.cpp

```diff
  18 |- #endif
  19 |- #endif
  20 |- 
```

> Username: joaquintides  
> Created_at: 2024-02-08 16:15:16 UTC  
> Updated_at: 2024-02-08 18:26:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1483241927  

Wasn't aware this PR removes this limitation. Very good!

> Username: k3DW  
> Created_at: 2024-02-10 17:14:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1485203886  

I didn't remove this, I just put this macro definition into "test/helpers/unordered.hpp" so it can be used by multiple test files


📁 include/boost/unordered/detail/allocator_constructed.hpp

```diff
  53 |+       /* constructs a stack-based object with the given allocator */
  54 |+       template <class Allocator, class T>
  55 |+       using allocator_constructed =
```

> Username: joaquintides  
> Created_at: 2024-02-08 16:24:16 UTC  
> Updated_at: 2024-02-08 18:26:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1483259039  

This is admittedly cosmetic, but I think it'd be sligthly more understandable if instead of `policy_constructed` and `allocator_constructed` we have just one class defined as:  
```cpp  
template <class Allocator, class T, class Policy = allocator_policy>  
class allocator_constructed;  
```

> Username: k3DW  
> Created_at: 2024-02-10 17:15:33 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1485204011  

I agree with this, I'll make the change


📁 doc/unordered/unordered_map.adoc

```diff
 800 |+ Pointers and references to elements are never invalidated. +
 801 |+ +
 802 |+ If `args...` is of the form `k,v`, it delays constructing the whole object until it is certain that an element should be inserted, using only the `k` argument to check.
```

> Username: joaquintides  
> Created_at: 2024-02-08 16:26:27 UTC  
> Updated_at: 2024-02-08 18:26:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1483262167  

* The optimization note should include the bit about "This optimization happens when `key_type` is move constructible or when the `k` argument is a `key_type`".  
* The optimization note should also go to `emplace_hint`.


> Username: joaquintides  
> Created_at: 2024-02-08 17:32:39 UTC  
> Updated_at: 2024-02-08 18:26:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1483351399  

There's a regression having to do with the handling of non-moveable keys (the other containers do fine):  
```cpp  
#include <boost/unordered_map.hpp>  
  
struct noncopyable_int  
{  
  noncopyable_int(int n_):n{n}{};  
  noncopyable_int(const noncopyable_int&)=delete;  
  
  friend bool operator==(const noncopyable_int&x, const noncopyable_int& y)  
  {  
    return x.n==y.n;  
  }  
  
  int n;  
};  
  
std::size_t hash_value(const noncopyable_int& x)  
{  
  return boost::hash<int>{}(x.n);  
}  
  
int main()  
{  
  boost::unordered_map<noncopyable_int,int> m;  
    
  // error C2660: 'std::pair<const K,T>::pair': function does not take 2 arguments  
  m.emplace(0,0);  
}  
```

> Username: k3DW  
> Created_at: 2024-02-10 17:54:07 UTC  
> Updated_at: 2024-02-10 17:54:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1485208668  

Good catch, I'll add this test and fix the regression


📁 include/boost/unordered/detail/map.hpp

```diff
  21 |         typedef P key_equal;
  22 |+         typedef K key_type;
  23 |         typedef K const const_key_type;
```

> Username: joaquintides  
> Created_at: 2024-02-08 18:26:08 UTC  
> Updated_at: 2024-02-09 08:32:58 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1483413076  

Why have you added this `typedef`? Seems obvious it was missing, but is there something that used to fail without it?

> Username: k3DW  
> Created_at: 2024-02-10 17:28:52 UTC  
> Updated_at: 2024-02-10 18:22:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#discussion_r1485205670  

~~Good catch, I used it in an earlier draft, but I forgot to remove it later. I'll remove it here, it's not needed~~  
I made a mistake, this gets used in `emplace_unique(converting_key, K&&, V&&)`


---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2024-02-10 23:58:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#issuecomment-1937359005  

An automated preview of the documentation is available at [https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2024-02-11 05:23:20 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#issuecomment-1937432012  

An automated preview of the documentation is available at [https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2024-02-11 17:33:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/230#issuecomment-1937817503  

An automated preview of the documentation is available at [https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://230.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
