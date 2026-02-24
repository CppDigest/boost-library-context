# #215 cxx03 deprecation [Merged]

> Username: cmazakas  
> Created at: 2023-09-26 17:50:46 UTC  
> Updated at: 2023-09-29 22:00:03 UTC  
> Merged at: 2023-09-29 22:00:00 UTC  
> Closed at: 2023-09-29 22:00:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/215  

Finally remove C++11 polyfills  
  
We remove the dependency on Boost.Tuple but indirectly support it by secretly supporting any generic tuple-type so long as it mimics `std::tuple`.  
  
The boostdep report before is:  
```  
Brief dependency report for unordered (sources on, tests off):  
  
# Primary dependencies  
  
assert  
config  
container_hash  
core  
move  
mp11  
predef  
preprocessor  
static_assert  
throw_exception  
tuple  
type_traits  
  
# Secondary dependencies  
  
describe  
```  
  
with this change, we see:  
```  
Brief dependency report for unordered (sources on, tests off):  
  
# Primary dependencies  
  
assert  
config  
container_hash  
core  
mp11  
predef  
throw_exception  
  
# Secondary dependencies  
  
describe  
type_traits  
static_assert  
```  
  
In total, this only removes the following dependencies from the project:  
- move  
- preprocessor  
- tuple  
  
with the potential to eventually see both TypeTraits and StaticAssert removed in the future.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-09-26 22:40:37 UTC  
> Updated_at: 2023-09-29 21:20:21 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#issuecomment-1736399760  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#215](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (554ce65) into [develop](https://app.codecov.io/gh/boostorg/unordered/commit/6b65c8f2308ca8162857ab359e6ac883b26e7f8e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6b65c8f) will **decrease** coverage by `0.02%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/215/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #215      +/-   ##  
===========================================  
- Coverage    98.14%   98.12%   -0.02%       
===========================================  
  Files          141      142       +1       
  Lines        19518    19502      -16       
===========================================  
- Hits         19156    19137      -19       
- Misses         362      365       +3       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/concurrent\_flat\_map.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvY29uY3VycmVudF9mbGF0X21hcC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/unordered/concurrent\_flat\_set.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvY29uY3VycmVudF9mbGF0X3NldC5ocHA=) | `100.00% <ø> (ø)` | |  
| [...ude/boost/unordered/detail/archive\_constructed.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2FyY2hpdmVfY29uc3RydWN0ZWQuaHBw) | `75.00% <100.00%> (+2.27%)` | :arrow_up: |  
| [include/boost/unordered/detail/fca.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZjYS5ocHA=) | `99.30% <100.00%> (-0.36%)` | :arrow_down: |  
| [...de/boost/unordered/detail/foa/concurrent\_table.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb25jdXJyZW50X3RhYmxlLmhwcA==) | `99.66% <100.00%> (ø)` | |  
| [include/boost/unordered/detail/foa/core.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9jb3JlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/unordered/detail/foa/node\_handle.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS9ub2RlX2hhbmRsZS5ocHA=) | `92.75% <ø> (-0.21%)` | :arrow_down: |  
| [include/boost/unordered/detail/foa/table.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS90YWJsZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/unordered/detail/implementation.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ltcGxlbWVudGF0aW9uLmhwcA==) | `99.22% <100.00%> (-0.01%)` | :arrow_down: |  
| [include/boost/unordered/detail/narrow\_cast.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL25hcnJvd19jYXN0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| ... and [39 more](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/unordered/pull/215/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6b65c8f...554ce65](https://app.codecov.io/gh/boostorg/unordered/pull/215?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Commented]

> Username: joaquintides  
> Created_at: 2023-09-27 18:05:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/215#pullrequestreview-1647146952  

For the record, I haven't reviewed the tests.

📁 include/boost/unordered/concurrent_flat_map.hpp

```diff
  69 |-       using key_equal = typename boost::type_identity<Pred>::type;
  70 |-       using allocator_type = typename boost::type_identity<Allocator>::type;
  67 |+       using hasher = typename boost::unordered::detail::type_identity<Hash>::type;
```

> Username: joaquintides  
> Created_at: 2023-09-27 16:37:17 UTC  
> Updated_at: 2023-09-28 07:31:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338900208  

I'm not entirely opposed to removing our dependency on Boost.TypeTraits, but looks like the gain is small since we're copy&pasting code from it, plus Boost.TypeTraits remains as a secondary dependency. Your opinion @pdimov?

> Username: pdimov  
> Created_at: 2023-09-27 18:08:11 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339012295  

The secondary dependency will eventually disappear, so it's still worth doing.


📁 include/boost/unordered/detail/archive_constructed.hpp

```diff
  64 | private:
  65 |-   typename aligned_storage<sizeof(T),alignment_of<T>::value>::type space;
  65 |+   opt_storage<T> space;
```

> Username: joaquintides  
> Created_at: 2023-09-27 16:39:26 UTC  
> Updated_at: 2023-09-27 18:05:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338903321  

The usual way to replace `aligned_storage` is by means of  
```cpp  
alignas(T) unsigned char space[sizeof(T)];  
```  
Is `opt_storage` gaining us something?

> Username: cmazakas  
> Created_at: 2023-09-27 18:23:57 UTC  
> Updated_at: 2023-09-27 18:23:58 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339031128  

It does the same thing, just abstracted away so that we can attach methods to it.  
  
In addition, it's automatically appropriately sized and aligned for our purposes. I got the trick from Peter, actually, and I've just chosen to stick with it.

> Username: joaquintides  
> Created_at: 2023-09-28 07:20:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339647702  

Ok!


📁 include/boost/unordered/detail/foa/concurrent_table.hpp

```diff
  90 | 
  91 |-   BOOST_STATIC_ASSERT(alignof(T)<=cacheline_size);
  91 |+   BOOST_UNORDERED_STATIC_ASSERT(alignof(T)<=cacheline_size);
```

> Username: joaquintides  
> Created_at: 2023-09-27 16:45:18 UTC  
> Updated_at: 2023-09-27 18:05:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338911045  

Same question as with Boost.TypeTraitrs, is it worth copying code from Boost.StaticAssert to remove the dependency?

> Username: pdimov  
> Created_at: 2023-09-27 18:10:27 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339015139  

Sure; there's no need in C++11 and above to include boost/static_assert.hpp. We only need a one-line macro and that only because we don't supply error messages in the assertions.

> Username: cmazakas  
> Created_at: 2023-09-27 18:27:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339035179  

Well, StaticAssert is pretty small so it's not the end of the world but in general, I'd prefer to not introduce an extra dependency for a native language feature. A goal I had in mind when doing this refactor was minimizing the output of `boostdep` so in turn this will reduce our dependency graph with package managers too.  
  
Assuming Conan ever figures out modular Boost.


📁 include/boost/unordered/detail/foa/core.hpp

```diff
1062 |-       std::is_trivially_constructible<group_type>::value
1063 |- #endif  
1054 |+         bool,std::is_trivially_constructible<group_type>::value
```

> Username: joaquintides  
> Created_at: 2023-09-27 16:49:02 UTC  
> Updated_at: 2023-09-27 18:05:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338915638  

There are two other similar workarounds for libstdc++<5.0 that haven't been removed (lines 2012 and 2061).  
  
Do we really want to forego compatibility with GCC<5.0? I'd say this is premature, given that GCC 4.7.x and 4.8.x appear as supported by Boost 1.83.

> Username: cmazakas  
> Created_at: 2023-09-27 18:38:59 UTC  
> Updated_at: 2023-09-27 18:39:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339049368  

I'm glad you brought this up because I wasn't sure about this change either.  
  
Inadvertently, we removed gcc<5.0 from our testing matrix with the new testing requirements.  
  
Back when I asked Glen how to handle the C++03 drop, he told me:  
> Basically when the time comes, don't use anything that is a compensation/workaround for missing or incomplete C++11 language support  
  
Now that I look back on it, I'm not sure if he meant to include library support in there as well but my operating assumption was: no more workarounds period.

> Username: cmazakas  
> Created_at: 2023-09-27 19:54:34 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339136351  

I think what we can do is, clean up the code so that we don't have any more of the workarounds and if a user a complains, we'll polyfill whatever we need from TypeTraits to make it all work. Otherwise, I'm perfectly content dropping gcc-4 as its `<type_traits>` header isn't up to par yet.

> Username: pdimov  
> Created_at: 2023-09-27 21:49:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339260069  

It's up to us whether to support gcc 4.8 and 4.9. I think we can afford to drop them in this day and age. Or if not, we can use `std::has_trivial_constructor` instead of `boost::has_trivial_constructor`.

> Username: pdimov  
> Created_at: 2023-09-27 21:50:05 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339260329  

E.g. https://github.com/boostorg/variant2/blob/a5cb0582d5669e86ee913f97c001677987023967/include/boost/variant2/variant.hpp#L510-L535

> Username: joaquintides  
> Created_at: 2023-09-28 07:44:25 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339676785  

@cmazakas I think copying Variant2's workaround is an excellent solution, as it's not dependent on Boost.TypeTraits.

> Username: joaquintides  
> Created_at: 2023-09-28 17:31:13 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1340473177  

@pdimov, why:  
```cpp  
std::is_copy_constructible<T>::value && std::has_trivial_copy_constructor<T>::value  
```  
instead of just:  
```cpp  
std::has_trivial_copy_constructor<T>::value  
```  
?  
Same with the other traits.

> Username: pdimov  
> Created_at: 2023-09-28 17:32:52 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1340475021  

I don't remember, but let me try to figure it out. There must have been a reason.

> Username: pdimov  
> Created_at: 2023-09-28 17:41:22 UTC  
> Updated_at: 2023-09-28 17:41:23 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1340483348  

I don't really know. I tried a few corner cases and it seems that the two traits agree. Let's just use `has_trivial_copy_constructor` by itself and see if it creates any trouble.

> Username: pdimov  
> Created_at: 2023-09-28 17:57:22 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1340499504  

Ah, found it. A deleted copy constructor is trivial. However, GCC 4.8 doesn't consider it one, which is why I didn't see it in my tests. 4.9 and above do (https://godbolt.org/z/evoq7daKq, https://godbolt.org/z/TK5cMMe1c).  
  
(Why is a deleted copy constructor trivial? Nobody knows. It was done to keep the copy constructors and assignment operators of `atomic<T>` trivial, supposedly for compatibility with C, but they aren't actually trivial, which necessitated patching this on the library side via [LWG issue 2424](https://www.open-std.org/jtc1/sc22/wg21/docs/lwg-defects.html#2424). Mysterious ways.)

> Username: joaquintides  
> Created_at: 2023-09-28 18:15:30 UTC  
> Updated_at: 2023-09-28 18:15:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1340518326  

Should then the following:  
```cpp  
#if BOOST_WORKAROUND(BOOST_LIBSTDCXX_VERSION,<50000)  
      /* std::is_trivially_default_constructible not provided */  
      std::has_trivial_default_constructor<group_type>::value  
#else  
      std::is_trivially_default_constructible<group_type>::value  
#endif   
```  
be rewritten as  
```cpp  
#if BOOST_WORKAROUND(BOOST_LIBSTDCXX_VERSION,<50000)  
      /* std::is_trivially_default_constructible not provided */  
      std::is_default_constructible<group_type>::value&&  
      std::has_trivial_default_constructor<group_type>::value  
#else  
      std::is_trivially_default_constructible<group_type>::value  
#endif   
```  
or do those deletion issues not apply here?

> Username: pdimov  
> Created_at: 2023-09-28 18:19:39 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1340522440  

GCC 4.9 considers a deleted default constructor trivial (https://godbolt.org/z/Wr6r8qrfx) so yes.

> Username: cmazakas  
> Created_at: 2023-09-28 18:20:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1340523282  

Actually, they are required. We seem to erroneously trigger `true` in cases that we shouldn't and we wind up segfaulting in the tests.


📁 include/boost/unordered/detail/fwd.hpp

```diff
  40 |+   namespace unordered {
  41 |+     using std::piecewise_construct;
  42 |+     using std::piecewise_construct_t;
```

> Username: joaquintides  
> Created_at: 2023-09-27 16:55:10 UTC  
> Updated_at: 2023-09-27 18:05:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338923485  

Why this here and not directly in `implementation.hpp`?  
  
Also, I think we shoud probably remove `BOOST_UNORDERED_SUN_WORKAROUNDS1` as we're not in a position to test the offending compilers anymore. If so, `fwd.hpp` can be effectively removed.


📁 include/boost/unordered/detail/implementation.hpp

```diff
 301 |-       template <typename T>
 302 |-       typename boost::add_lvalue_reference<T>::type make();
 282 |+       template <typename T> typename std::add_lvalue_reference<T>::type make();
```

> Username: joaquintides  
> Created_at: 2023-09-27 17:10:19 UTC  
> Updated_at: 2023-09-27 18:05:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338942086  

I think `choice3`-`choice9` are no longer used.

---

📁 include/boost/unordered/detail/implementation.hpp

```diff
 558 |+           (void)tuple;
 559 |+           using std::get;
 560 |           return std::tuple<typename std::add_lvalue_reference<Args>::type...>(
```

> Username: joaquintides  
> Created_at: 2023-09-27 17:12:29 UTC  
> Updated_at: 2023-09-27 18:05:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338944552  

You can do `return {...}` without having to repeat the return type.

> Username: cmazakas  
> Created_at: 2023-09-27 20:01:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339143489  

Seems like older versions of clang don't like this, I get:  
```  
./boost/unordered/detail/implementation.hpp:535:18: error: chosen constructor is explicit in copy-initialization  
          return {get<Is>(tuple)...};  
```  
from the Drone CI runners.

---

📁 include/boost/unordered/detail/implementation.hpp

```diff
1559 |+           typedef
1560 |+             typename boost::allocator_propagate_on_container_copy_assignment<
1561 |+               node_allocator_type>::type pocca;
```

> Username: joaquintides  
> Created_at: 2023-09-27 17:19:36 UTC  
> Updated_at: 2023-09-27 18:05:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338953395  

This is absolutely pedantic, but `boost::allocator_propagate_on_container_copy_assignment<node_allocator_type>::type` may not be default constructible.

> Username: cmazakas  
> Created_at: 2023-09-27 18:55:59 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339069926  

I just double-checked the code, I'm not sure we're ever default constructing it here though.  
  
`pocca` here should just be a typedef and then we only ever access `pocca::value` in the corresponding function call.  
  
The standard then mandates that this type is identical to `std::true_type`/`std::false_type`  
  
https://eel.is/c++draft/allocator.requirements.general#itemdecl:32  
  
I think this is all kosher then.

> Username: joaquintides  
> Created_at: 2023-09-28 07:27:48 UTC  
> Updated_at: 2023-09-28 07:27:55 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339656666  

Please disregard, I seriously misread the code :-) Everything's fine.

---

📁 include/boost/unordered/detail/implementation.hpp

```diff
1763 |             for (node_pointer* pp = boost::addressof(itb->next); *pp;
2417 |-                 pp = boost::addressof((*pp)->next)) {
1764 |+                  pp = boost::addressof((*pp)->next)) {
```

> Username: joaquintides  
> Created_at: 2023-09-27 17:20:19 UTC  
> Updated_at: 2023-09-27 18:05:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338954220  

`std::addressof`? This happens in other places.

> Username: cmazakas  
> Created_at: 2023-09-27 18:58:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339072312  

I was trying to minimize the overall diffs but we were forced to use `std::addressof` in one spot because the Core impl is bugged on gcc-5 and gcc-6.  
  
For stylistic consistency, I'll replace all usages of `boost::addressof` with their std counterpart.


📁 include/boost/unordered/detail/opt_storage.hpp

```diff
   1 |+ #ifndef BOOST_UNORDERED_DETAIL_OPT_STORAGE_HPP
```

> Username: joaquintides  
> Created_at: 2023-09-27 17:24:04 UTC  
> Updated_at: 2023-09-27 18:05:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338958735  

(C) and license note missing.


📁 include/boost/unordered/detail/prime_fmod.hpp

```diff
   1 | // Copyright (C) 2022 Joaquin M Lopez Munoz.
   2 |- // Copyright (C) 2022 Christian Mazakas
   2 |+ // Copyright (C) 2022-2023 Christian Mazakas
```

> Username: joaquintides  
> Created_at: 2023-09-27 17:50:07 UTC  
> Updated_at: 2023-09-28 07:30:45 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338990336  

This is rather large refactoring and I'm a little nervous some off-by-one error or similar may have slipped in the process (though I don't see any problem from a visual inspection). I may write some testing code (not to be included in the lib's test suite) to make sure everything's fine.

> Username: cmazakas  
> Created_at: 2023-09-27 19:03:56 UTC  
> Updated_at: 2023-09-27 19:03:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339079206  

Ha ha, I'm nervous too.  
  
But this is why we have betas and release candidate testing and also we can apply whatever patches are needed to the official release.

> Username: joaquintides  
> Created_at: 2023-09-28 18:43:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1340544044  

I've run this:  
```cpp  
#include <boost/unordered/detail/prime_fmod.hpp>  
#include "prime_fmod_new.hpp"  
  
#include <algorithm>  
#include <iostream>  
#include <utility>  
  
#define ASSERT(...)         \  
do{                         \  
  if(!(__VA_ARGS__)){       \  
    std::cout<<"Fehler!\n"; \  
    std::abort();           \  
  }                         \  
}                           \  
while(0)  
  
int main()  
{  
  using pfmod_size=boost::unordered::detail::prime_fmod_size<>;  
  using pfmod_size_new=boost::unordered_new::detail::prime_fmod_size<>;  
  
  ASSERT(pfmod_size::sizes_len==pfmod_size_new::sizes_len);  
  ASSERT(std::equal(  
    pfmod_size::sizes,pfmod_size::sizes+pfmod_size::sizes_len,  
    pfmod_size_new::sizes));  
  
#if defined(BOOST_UNORDERED_FCA_HAS_64B_SIZE_T)  
  ASSERT(pfmod_size::inv_sizes32_len==pfmod_size_new::inv_sizes32_len);  
  ASSERT(std::equal(  
    pfmod_size::inv_sizes32,pfmod_size::inv_sizes32+pfmod_size::inv_sizes32_len,  
    pfmod_size_new::inv_sizes32));  
#endif  
  
  for(std::size_t n=0;n<10'000'000;++n){  
    std::size_t s=n*n*100'002'223;  
    ASSERT(pfmod_size::size_index(s)==pfmod_size_new::size_index(s));  
  }  
  
  for(std::size_t size_index=0;size_index<pfmod_size::sizes_len;++size_index){  
    std::cout<<"size_index: "<<size_index<<"\n";  
    for(std::size_t n=0;n<10'000'000;++n){  
      std::size_t hash=n*n*(size_index+1)*100'002'223;  
      ASSERT(  
        pfmod_size::position(hash,size_index)==  
        pfmod_size_new::position(hash,size_index));  
    }  
  }  
  std::cout<<"Alles gut!"<<std::endl;  
}  
```  
and Alles war gut, so we can resolve this comment. Thank you!

---

📁 include/boost/unordered/detail/prime_fmod.hpp

```diff
 196 |+       // static data member (since C++11)(until C++17) is odr-used, a definition
 197 |+       // at namespace scope is still required, but it cannot have an
 198 |+       // initializer.
```

> Username: joaquintides  
> Created_at: 2023-09-27 17:52:15 UTC  
> Updated_at: 2023-09-27 18:05:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338992830  

This requirement is actually deprecated in C++17, so it may be a good idea to conditionally remove the out-of-class definitions in that case.

> Username: cmazakas  
> Created_at: 2023-09-27 19:04:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339079805  

I was thinking about this as well, glad to see we're on the same page.  
  
I didn't make it conditional outright because compilers seem to handle it fine but it is sloppier, I agree.

> Username: pdimov  
> Created_at: 2023-09-27 21:54:14 UTC  
> Updated_at: 2023-09-27 21:54:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339263918  

The definitions are required when inline variables aren't supported, and deprecated when they are.


📁 include/boost/unordered/detail/static_assert.hpp

```diff
   1 |+ #ifndef BOOST_UNORDERED_DETAIL_STATIC_ASSERT_HPP
```

> Username: joaquintides  
> Created_at: 2023-09-27 17:52:59 UTC  
> Updated_at: 2023-09-27 18:05:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338993702  

(C) and license note missing.


📁 include/boost/unordered/detail/type_traits.hpp

```diff
  54 |-         typename boost::make_void<typename T::is_transparent>::type>
  55 |-           : public boost::true_type
  84 |+         typename boost::unordered::detail::make_void<typename T::is_transparent>::type>
```

> Username: joaquintides  
> Created_at: 2023-09-27 17:54:23 UTC  
> Updated_at: 2023-09-27 18:05:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338995510  

Stylistic: now that we're in C++11 or later, all uses of `typename make_void<...>::type` in the code can be replaced by `void_t<...>`.


📁 include/boost/unordered/unordered_map.hpp

```diff
  27 |- #if !defined(BOOST_NO_CXX11_HDR_INITIALIZER_LIST)
  22 |+ #include <boost/core/explicit_operator_bool.hpp>
  23 |+ #include <boost/functional/hash.hpp>
```

> Username: joaquintides  
> Created_at: 2023-09-27 17:57:39 UTC  
> Updated_at: 2023-09-27 18:05:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1338999413  

Stylistic:  `#include <boost/functional/hash[_fwd].hpp>` can be replaced by `#include <boost/container_hash/hash[_fwd].hpp>` everywhere in the code.

> Username: cmazakas  
> Created_at: 2023-09-27 19:15:06 UTC  
> Updated_at: 2023-09-27 19:15:07 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#discussion_r1339092902  

That's funny, that's how old Unordered really is :P


---

## Comment 3

> Username: joaquintides  
> Created_at: 2023-09-28 09:42:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#issuecomment-1738821157  

Before I forget, [libraries.json](https://github.com/cmazakas/unordered/blob/cxx03_deprecation/meta/libraries.json) need be updated to `"cxxstd": "11"`. It's a good opportunity to update the maintainers info, also.

---

## Comment 4

> Username: pdimov  
> Created_at: 2023-09-28 09:48:59 UTC  
> Url: https://github.com/boostorg/unordered/pull/215#issuecomment-1738831363  

The maintainer info should be updated immediately; no need to wait for this branch. :-)

---
