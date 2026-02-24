# #749 object::stable_erase [Merged]

> Username: gummif  
> Created at: 2022-08-29 17:35:04 UTC  
> Updated at: 2022-09-18 00:57:36 UTC  
> Merged at: 2022-09-18 00:57:13 UTC  
> Closed at: 2022-09-18 00:57:13 UTC  
> Url: https://github.com/boostorg/json/pull/749  

Support for erasing an element without reordering. This is part one of support https://github.com/boostorg/json/issues/748, part two would be stable_insert.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-08-29 17:40:52 UTC  
> Url: https://github.com/boostorg/json/pull/749#issuecomment-1230640087  

impressive work :) you figured out how `object` is implemented...

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-29 17:41:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/749#pullrequestreview-1089119816  

📁 include/boost/json/object.hpp

```diff
1173 |+         iterator (which is valid but cannot be dereferenced)
1174 |+         cannot be used as a value for `pos`.
1175 |+         All references and iterators are invalidated.
```

> Username: vinniefalco  
> Created_at: 2022-08-29 17:41:57 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r957631629  

Is this true? Or does it only invalidate iterators from pos and later?

> Username: gummif  
> Created_at: 2022-08-29 20:05:54 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r957748283  

No not technically true, I update the spec.

> Username: gummif  
> Created_at: 2022-08-29 20:06:54 UTC  
> Updated_at: 2022-08-29 20:06:55 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r957749069  

However for the key erase function I don't see a reason to be more specific since we are not passing in an iterator.

> Username: vinniefalco  
> Created_at: 2022-08-29 22:35:39 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r957848668  

agreed


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-08-29 17:48:23 UTC  
> Updated_at: 2022-09-16 21:28:27 UTC  
> Url: https://github.com/boostorg/json/pull/749#issuecomment-1230648031  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/749?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#749](https://codecov.io/gh/boostorg/json/pull/749?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (47f2ad5) into [develop](https://codecov.io/gh/boostorg/json/commit/2cbc263ced1e2e8151fd8096e1f4e8b87eaeaa28?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2cbc263) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/749/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/749?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #749   +/-   ##  
========================================  
  Coverage    99.13%   99.13%             
========================================  
  Files           69       69             
  Lines         6556     6584   +28       
========================================  
+ Hits          6499     6527   +28       
  Misses          57       57             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/749?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/object.hpp](https://codecov.io/gh/boostorg/json/pull/749/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/object.ipp](https://codecov.io/gh/boostorg/json/pull/749/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/749?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/749?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2cbc263...47f2ad5](https://codecov.io/gh/boostorg/json/pull/749?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-08-29 18:49:36 UTC  
> Url: https://github.com/boostorg/json/pull/749#issuecomment-1230719293  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/749/pullrequest-condensed-de1d263.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/749/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/749/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-08-29 21:19:34 UTC  
> Url: https://github.com/boostorg/json/pull/749#issuecomment-1230873227  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/749/pullrequest-condensed-b1df6a2.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/749/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/749/pullrequest.html)

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-08-29 22:37:25 UTC  
> Url: https://github.com/boostorg/json/pull/749#issuecomment-1230942851  

@grisumbras I guess I have no objection to offering this feature. If the user wants to opt-in to preserving order at a computational cost, I can't see a downside. But its up to you, maybe there's something I haven't thought of? What does Peter think?

---

## Review 7 [Commented]

> Username: sehe  
> Created_at: 2022-09-02 08:43:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/749#pullrequestreview-1094506002  

Got nerd sniped by this PR. Nice work :)

📁 include/boost/json/object.hpp

```diff
1616 |         key_value_pair* last) noexcept;
1617 |+ 
1618 |+     inline
```

> Username: sehe  
> Created_at: 2022-09-02 08:26:43 UTC  
> Updated_at: 2022-09-02 08:43:51 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r961427456  

Could this use a little javadoc even though it's internal?

> Username: vinniefalco  
> Created_at: 2022-09-02 09:26:34 UTC  
> Updated_at: 2022-09-02 16:50:21 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r961482321  

eh..   
  
  
by the way docca supports `@param first, last The range to destroy` now

---

📁 include/boost/json/object.hpp

```diff
1184 |+         No-throw guarantee.
1185 |+ 
1186 |+         @return An iterator following the last removed element.
```

> Username: sehe  
> Created_at: 2022-09-02 08:28:40 UTC  
> Updated_at: 2022-09-02 08:43:51 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r961429724  

"last" seems redundant here

---

📁 include/boost/json/object.hpp

```diff
1172 |+         be valid and dereferenceable. Thus the @ref end()
1173 |+         iterator (which is valid but cannot be dereferenced)
1174 |+         cannot be used as a value for `pos`.
```

> Username: sehe  
> Created_at: 2022-09-02 08:31:40 UTC  
> Updated_at: 2022-09-02 08:43:51 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r961432787  

The sentences "Thus the @ref end() iterator (which is valid but cannot be dereferenced) cannot be used as a value for `pos`." is redundant.  
  
If this is expected to be a gotcha (?) I'd move it to a note below.

> Username: gummif  
> Created_at: 2022-09-02 13:13:06 UTC  
> Updated_at: 2022-09-02 13:13:07 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r961666070  

It looks like this is verbatim from https://en.cppreference.com/w/cpp/container/vector/erase. Moved to note section.


---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-09-02 14:24:46 UTC  
> Url: https://github.com/boostorg/json/pull/749#issuecomment-1235568537  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/749/pullrequest-condensed-0c49f1b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/749/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/749/pullrequest.html)

---

## Review 9 [Commented]

> Username: grisumbras  
> Created_at: 2022-09-13 04:21:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/749#pullrequestreview-1105102060  

📁 include/boost/json/impl/object.ipp

```diff
 549 |+ auto
 550 |+ object::
 551 |+ stable_erase(const_iterator pos) noexcept ->
```

> Username: grisumbras  
> Created_at: 2022-09-13 04:19:30 UTC  
> Updated_at: 2022-09-13 04:21:27 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r969134277  

`stable_erase` and `erase` share quite a lot of code. A feel like a better approach is having a private  function template `iterator do_erase(const_iterator pos, F get_pb)` that ends with  
```c++  
auto result = p;  
while( p != end() )  
{  
    auto pb = get_pb(p);  
    reindex_relocate(pb, p);  
    p = pb;  
}  
return result;  
```  
`erase` would be implemented like `return do_erase(pos, [this](iterator) { return end(); })`  
`stable_erase` would be implemented like `return do_erase(pos, [](iterator p) { return ++p; })`

> Username: sehe  
> Created_at: 2022-09-14 21:29:09 UTC  
> Updated_at: 2022-09-14 21:29:10 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r971312143  

I looked at the same a while back. A subtle difference (easy to miss) is that memcpy is memmov here and acts on multiple elements, instead of just 1. I still feel that it can probably be a shared implementation, but it require some scrutiny.

> Username: gummif  
> Created_at: 2022-09-16 21:13:38 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r973395870  

This has been refactored to a common implementation.

> Username: vinniefalco  
> Created_at: 2022-09-16 21:40:49 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r973420927  

For the record I was fine with it before the refactoring


📁 include/boost/json/object.hpp

```diff
1177 |+         included, are invalidated. Other iterators and references
1178 |+         are not invalidated.
1179 |+         The relative order of non-erased elements is preserved.
```

> Username: grisumbras  
> Created_at: 2022-09-13 04:20:14 UTC  
> Updated_at: 2022-09-13 04:21:27 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r969134567  

s/non-erased/remaining/

---

📁 include/boost/json/object.hpp

```diff
1203 |+         Remove the element which matches `key`, if it exists.
1204 |+         All references and iterators are invalidated.
1205 |+         The relative order of non-erased elements is preserved.
```

> Username: grisumbras  
> Created_at: 2022-09-13 04:20:45 UTC  
> Updated_at: 2022-09-13 04:21:27 UTC  
> Url: https://github.com/boostorg/json/pull/749#discussion_r969134766  

s/non-erased/remaining/


---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-09-16 22:24:38 UTC  
> Url: https://github.com/boostorg/json/pull/749#issuecomment-1249910162  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/749/pullrequest-condensed-43cdc5e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/749/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/749/pullrequest.html)

---

## Comment 11

> Username: grisumbras  
> Created_at: 2022-09-18 00:57:36 UTC  
> Url: https://github.com/boostorg/json/pull/749#issuecomment-1250165471  

Thanks for the feature.

---
