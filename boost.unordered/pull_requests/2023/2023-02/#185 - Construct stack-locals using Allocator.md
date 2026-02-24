# #185 Construct stack-locals using Allocator [Merged]

> Username: cmazakas  
> Created at: 2023-02-27 23:13:47 UTC  
> Updated at: 2023-03-06 21:39:09 UTC  
> Merged at: 2023-03-06 20:38:14 UTC  
> Closed at: 2023-03-06 20:38:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/185  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-02-28 04:12:07 UTC  
> Updated_at: 2023-03-06 17:49:24 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#issuecomment-1447549488  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#185](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6c5b5b3) into [develop](https://codecov.io/gh/boostorg/unordered/commit/d90bf0c4388f72df11b5c347357fcbc1e8e64de4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d90bf0c) will **decrease** coverage by `0.05%`.  
> The diff coverage is `92.42%`.  
  
> :exclamation: Current head 6c5b5b3 differs from pull request most recent head e56cb44. Consider uploading reports for the commit e56cb44 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/185/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #185      +/-   ##  
===========================================  
- Coverage    97.90%   97.85%   -0.05%       
===========================================  
  Files           89       91       +2       
  Lines        13777    13849      +72       
===========================================  
+ Hits         13488    13552      +64       
- Misses         289      297       +8       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...nclude/boost/unordered/detail/foa/element\_type.hpp](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9lbGVtZW50X3R5cGUuaHBw) | `66.66% <66.66%> (ø)` | |  
| [include/boost/unordered/detail/foa/node\_handle.hpp](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9ub2RlX2hhbmRsZS5ocHA=) | `92.95% <90.90%> (-0.20%)` | :arrow_down: |  
| [test/unordered/uses\_allocator.cpp](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvdXNlc19hbGxvY2F0b3IuY3Bw) | `93.33% <93.33%> (ø)` | |  
| [include/boost/unordered/detail/foa.hpp](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS5ocHA=) | `99.83% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/unordered/unordered\_flat\_map.hpp](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX2ZsYXRfbWFwLmhwcA==) | `99.57% <100.00%> (+0.01%)` | :arrow_up: |  
| [include/boost/unordered/unordered\_flat\_set.hpp](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX2ZsYXRfc2V0LmhwcA==) | `99.42% <100.00%> (ø)` | |  
| [include/boost/unordered/unordered\_node\_map.hpp](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX25vZGVfbWFwLmhwcA==) | `99.33% <100.00%> (+0.01%)` | :arrow_up: |  
| [include/boost/unordered/unordered\_node\_set.hpp](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX25vZGVfc2V0LmhwcA==) | `99.14% <100.00%> (-0.01%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d90bf0c...e56cb44](https://codecov.io/gh/boostorg/unordered/pull/185?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Commented]

> Username: joaquintides  
> Created_at: 2023-03-02 18:48:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/185#pullrequestreview-1322273792  

Additionally to the inline comments:  
  
* ~~I think there's some confusion with `al` parameters in the `construct`/`destroy` overloads in the type policies; we should stick to one of these policies:~~  
  * ~~`al` is properly bound to the type passed (my preferred option).~~  
  * ~~`al` is bound to some fixed type (say `value_type`) everywhere and rebinding is done inside `construct`/`destroy` as appropriate.~~  
* ~~The explanation for type policies in https://github.com/boostorg/unordered/blob/fix/uses-allocator/include/boost/unordered/detail/foa.hpp#L1151-L1208 needs to be updated (type policies' functionality has been expanded).~~  
* ~~Consider attaching a link in the conversation to the benchmarks that show that performance is preserved.~~  
* ~~Consider adding a bullet to the release notes with this fix.~~

📁 include/boost/unordered/detail/foa.hpp

```diff
1131 | 
1132 |+ template <class T>
1133 |+ union storage
```

> Username: joaquintides  
> Created_at: 2023-03-02 17:13:06 UTC  
> Updated_at: 2023-03-02 18:48:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123453717  

`storage` feels like too generic a name... maybe `uninitialized_storage`?

---

📁 include/boost/unordered/detail/foa.hpp

```diff
1135 |+   T t_;
1136 |+   storage(){}
1137 |+   ~storage(){}
```

> Username: joaquintides  
> Created_at: 2023-03-02 17:13:22 UTC  
> Updated_at: 2023-03-02 18:48:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123454153  

Why do we need a destructor?

> Username: cmazakas  
> Created_at: 2023-03-02 19:05:52 UTC  
> Updated_at: 2023-03-02 19:05:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123587585  

It seems to be required when the type has a non-trivial destructor.   
  
The following example demonstrates:  
https://godbolt.org/z/1s5zP738b

> Username: joaquintides  
> Created_at: 2023-03-04 09:52:41 UTC  
> Updated_at: 2023-03-04 09:52:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1125430772  

Understood, thank you!

---

📁 include/boost/unordered/detail/foa.hpp

```diff
1449 |+       emplace_type,element_type
1450 |+     >::type;
1451 |+ 
```

> Username: joaquintides  
> Created_at: 2023-03-02 17:20:15 UTC  
> Updated_at: 2023-03-02 18:48:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123464999  

I love your naming of `emplace_type` and `insert_type`, it's a very fitting terminology.

---

📁 include/boost/unordered/detail/foa.hpp

```diff
1450 |+     >::type;
1451 |+ 
1452 |+     using alloc_insert_type=typename std::conditional<
```

> Username: joaquintides  
> Created_at: 2023-03-02 17:21:02 UTC  
> Updated_at: 2023-03-02 18:48:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123466041  

This type looks like a leftover as it's exactly the same as `insert_type`.

---

📁 include/boost/unordered/detail/foa.hpp

```diff
1139 |+ 
1140 |+ template <class TypePolicy,class A,class T>
1141 |+ struct drop_guard
```

> Username: joaquintides  
> Created_at: 2023-03-02 18:28:38 UTC  
> Updated_at: 2023-03-02 18:48:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123548471  

I know I asked for this to be defined outside `table`, but upon re-reviewing I think it makes more sense to rename this as `destroy_on_exit` and define it in-class alongside `clear_on_exit` and `erase_on_exit`.


📁 include/boost/unordered/detail/foa/node_handle.hpp

```diff
 108 |+       element_type x;
 109 |+       x.p=p_;
 110 |+ 
```

> Username: joaquintides  
> Created_at: 2023-03-02 17:23:57 UTC  
> Updated_at: 2023-03-02 18:48:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123470229  

You're doing this contortion (creating a local `element_type` so that you can later destroy `p_` through it) several times: this looks like an indication that you're probably better off storing an `element_type` in `node_handle_base` rather than a `value_type*`.

---

📁 include/boost/unordered/detail/foa/node_handle.hpp

```diff
 150 |-         type_policy::destroy(al(),p_);
 153 |+         element_type x;
 154 |+         x.p=p_;
```

> Username: joaquintides  
> Created_at: 2023-03-02 17:24:19 UTC  
> Updated_at: 2023-03-02 18:48:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123470707  

Ditto


📁 include/boost/unordered/unordered_flat_map.hpp

```diff
  74 |+         template <class A, class... Args>
  75 |+         static void construct(A& al, element_type* p, Args&&... args)
  76 |+         {
```

> Username: joaquintides  
> Created_at: 2023-03-02 18:20:21 UTC  
> Updated_at: 2023-03-02 18:48:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123539163  

This looks inconsistent: why are you rebinding here and not in `construct(A& al, init_type* p, Args&&... args)`? I understand the contract is that the caller passes an appropriately rebound allocator.

> Username: joaquintides  
> Created_at: 2023-03-02 18:22:32 UTC  
> Updated_at: 2023-03-02 18:48:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123542493  

Also, consider renaming `construct(A& al, element_type* p, Args&&... args)` and `destroy(A& al, element_type* p)` to `construct(A& al, value_type* p, Args&&... args)` and `destroy(A& al, value_type* p)`: for flat containers, `element_type` is a mere alias to `value_type`  so it looks nicer if `element_type` does not appear in the policy except for its definition (unlike in node containers, where `element_type` receives special treatment).

> Username: cmazakas  
> Created_at: 2023-03-03 23:15:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1125123942  

Good catch. I was erroneously rebinding, likely as an artifact of early passes at this refactor.  
  
The code has been updated such that it no longer needs to rebind at all. We know that our Allocator is properly bound to allocate, deallocate value_type. The construct(), destroy() methods are supposed to work with any and all types without the need for rebinding.

---

📁 include/boost/unordered/unordered_flat_map.hpp

```diff
  57 |+         {
  58 |+           return {std::get<0>(std::move(x)), std::get<1>(std::move(x))};
  59 |+         }
```

> Username: joaquintides  
> Created_at: 2023-03-02 18:23:54 UTC  
> Updated_at: 2023-03-02 18:48:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123543710  

Why not `{std::move(x.first), std::move(x.second)};`?  
  
Same for other places where this construct appears.

> Username: pdimov  
> Created_at: 2023-03-02 18:55:20 UTC  
> Updated_at: 2023-03-02 18:55:21 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123575246  

That's not the same, see https://gcc.gnu.org/bugzilla/show_bug.cgi?id=108952

> Username: pdimov  
> Created_at: 2023-03-02 18:55:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123575728  

Ah wait, it's the same here. :-)


📁 include/boost/unordered/unordered_node_map.hpp

```diff
 128 |+ 
 129 |+             boost::allocator_deallocate(
 130 |+               al,pointer_traits::pointer_to(*(p->p)),1);
```

> Username: joaquintides  
> Created_at: 2023-03-02 18:34:29 UTC  
> Updated_at: 2023-03-02 18:48:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123554108  

Why not just `boost::allocator_deallocate(al,p->p,1)`?

> Username: cmazakas  
> Created_at: 2023-03-02 22:44:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123814130  

In our cases, the `element_type` stores a `value_type*` which we can't deallocate through if the Allocator is using fancy pointers.  
  
I think it's easiest for us to store `value_type*` and convert from raw pointers to fancy pointers only in these certain instances.

> Username: joaquintides  
> Created_at: 2023-03-04 10:19:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1125433937  

Yes, I agree. Thank you!

---

📁 include/boost/unordered/unordered_node_map.hpp

```diff
 152 |+             destroy(al, p->p);
 153 |+             boost::allocator_deallocate(
 154 |+               al,pointer_traits::pointer_to(*(p->p)), 1);
```

> Username: joaquintides  
> Created_at: 2023-03-02 18:35:06 UTC  
> Updated_at: 2023-03-02 18:48:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123554644  

Why not just `boost::allocator_deallocate(al,p->p,1)`?

> Username: joaquintides  
> Created_at: 2023-03-02 18:40:22 UTC  
> Updated_at: 2023-03-02 18:48:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123559412  

Also, is not `al` supposed to be bound to `element_type`?

> Username: cmazakas  
> Created_at: 2023-03-02 23:42:16 UTC  
> Updated_at: 2023-03-02 23:42:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1123871062  

`al` shouldn't be bound to `element_type` because in all actuality, we're only allocating and deallocating the `type_policy::value_type`.  
  
If we wanted to harden these measures, we should probably add:  
```cpp  
BOOST_STATIC_ASSERT(  
  std::is_same<  
    typename boost::allocator_value_type<A>::type,  
    type_policy::value_type  
  >::value);  
```

> Username: joaquintides  
> Created_at: 2023-03-04 10:20:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1125434038  

I think it's ok as it's now, no need to go extra picky and do the static assert.


---

## Comment 3

> Username: pdimov  
> Created_at: 2023-03-02 18:51:39 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#issuecomment-1452387418  

`construct` and `destroy` don't require rebinding if I'm not mistaken.

---

## Comment 4

> Username: cmazakas  
> Created_at: 2023-03-02 23:37:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#issuecomment-1452726561  

> `construct` and `destroy` don't require rebinding if I'm not mistaken.  
  
Actually, good point; this is correct.  
  
I looked into a bit more and yeah, an Allocator's value_type only matters in the case of allocate(), deallocate().  
  
In these cases, our allocator is already bound to `value_type` which means, we don't need any rebinding at all!

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-03-03 20:06:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#issuecomment-1454067588  

An automated preview of the documentation is available at [https://185.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://185.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 6 [Commented]

> Username: joaquintides  
> Created_at: 2023-03-04 10:18:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/185#pullrequestreview-1324986317  

📁 include/boost/unordered/detail/foa.hpp

```diff
1660 |+   struct destroy_on_exit
1661 |+   {
1662 |+     using type_policy=TypePolicy;
```

> Username: joaquintides  
> Created_at: 2023-03-04 10:18:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1125433881  

Is this `using` needed?


---

## Review 7 [Commented]

> Username: joaquintides  
> Created_at: 2023-03-04 10:52:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/185#pullrequestreview-1324995553  

📁 include/boost/unordered/detail/foa.hpp

```diff
1176 |+  *     and element_type. For flat containers, these are often all synonyms for
1177 |+  *     each other but for the node-based map, each one is a distinct type. These
1178 |+  *     are used for allocator-aware construction and destruction of the types
```

> Username: joaquintides  
> Created_at: 2023-03-04 10:52:03 UTC  
> Updated_at: 2023-03-04 10:52:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#discussion_r1125441862  

It is not the case that for flat containers `value_type`, `init_type` and `element_type` are the same. In fact:  
* For set containers, `init_type` and `value_type` are the same, for map containers they're not.  
* For flat containers, `value_type` and `element_type` are the same, for node containers they're not,  
  
Anyway, I'd just remove the "For flat containers [...] during insertion" part altogether, as it's redundant with the rest of the information already provided elsewhere in the comment.


---

## Comment 8

> Username: joaquintides  
> Created_at: 2023-03-04 18:49:38 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#issuecomment-1454839299  

Comparison between latest n-running benchmark for `fix/uses_allocator` and baseline:  
  
https://github.com/boostorg/boost_unordered_benchmarks/compare/f2e08733074a30b7e26bfe5e0dd5e5653d836464...a031e1b61c5f18532d56ff14814a4b3d87ed7660

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-03-06 17:56:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#issuecomment-1456650359  

An automated preview of the documentation is available at [https://185.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://185.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 10

> Username: pdimov  
> Created_at: 2023-03-06 18:13:25 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#issuecomment-1456689665  

What are we doing with this PR now that the release is closed for major changes?

---

## Comment 11

> Username: joaquintides  
> Created_at: 2023-03-06 18:14:32 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#issuecomment-1456693086  

This is a bug fix, we're not allocator aware without it.

---

## Comment 12

> Username: pdimov  
> Created_at: 2023-03-06 18:22:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#issuecomment-1456711958  

Not sure we have enough time to merge that into develop and then to master before the beta freeze.

---

## Comment 13

> Username: joaquintides  
> Created_at: 2023-03-06 18:24:51 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#issuecomment-1456716831  

As for time, we have it: I can merge as soon as Drone CI passes, and then we have more than one day to let the develop CI pass and merge to master.

---

## Comment 14

> Username: pdimov  
> Created_at: 2023-03-06 19:25:22 UTC  
> Url: https://github.com/boostorg/unordered/pull/185#issuecomment-1456821896  

All right then.

---
