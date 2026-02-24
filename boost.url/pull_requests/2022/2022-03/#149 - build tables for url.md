# #149 build tables for url [Closed]

> Username: alandefreitas  
> Created at: 2022-03-22 02:44:14 UTC  
> Updated at: 2022-06-08 13:37:21 UTC  
> Closed at: 2022-03-25 16:16:20 UTC  
> Url: https://github.com/boostorg/url/pull/149  

Work in progress. We're building the tables but not using the tables yet.  
  
fix #146

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-03-22 05:47:43 UTC  
> Updated_at: 2022-03-25 04:38:53 UTC  
> Url: https://github.com/boostorg/url/pull/149#issuecomment-1074753083  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/149?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#149](https://codecov.io/gh/CPPAlliance/url/pull/149?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (c66093d) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/fbc51ed52412d1421767effa92951ff417c1691f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (fbc51ed) will **increase** coverage by `0.11%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/149/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/149?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #149      +/-   ##  
===========================================  
+ Coverage    96.80%   96.92%   +0.11%       
===========================================  
  Files          110      112       +2       
  Lines         6197     6372     +175       
===========================================  
+ Hits          5999     6176     +177       
+ Misses         198      196       -2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/149?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url.hpp](https://codecov.io/gh/CPPAlliance/url/pull/149/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/params\_table.hpp](https://codecov.io/gh/CPPAlliance/url/pull/149/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3BhcmFtc190YWJsZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/segments\_table.hpp](https://codecov.io/gh/CPPAlliance/url/pull/149/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3NlZ21lbnRzX3RhYmxlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/url.ipp](https://codecov.io/gh/CPPAlliance/url/pull/149/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmwuaXBw) | `98.54% <100.00%> (+0.32%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/149?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/149?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [fbc51ed...c66093d](https://codecov.io/gh/CPPAlliance/url/pull/149?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 05:56:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/149#pullrequestreview-916692738  

📁 include/boost/url/detail/config.hpp

```diff
  15 |- #include <limits.h>
  15 |+ #include <climits>
  16 |+ #include <cstdint>
```

> Username: vinniefalco  
> Created_at: 2022-03-22 05:56:29 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r831794058  

I really try to keep the number of includes in this file down to a minimum, because it is included everywhere. That's why I included limits.h instead of cstdint and climits.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 05:59:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/149#pullrequestreview-916694305  

📁 include/boost/url/impl/url.ipp

```diff
2274 | url::
2275 | build_tab() noexcept
```

> Username: vinniefalco  
> Created_at: 2022-03-22 05:59:01 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r831795238  

this should follow the fields_view_base interface of `write_table`:   
https://github.com/CPPAlliance/http_proto/blob/3168cbadff093a837f45a4414656951d154f2486/include/boost/http_proto/impl/fields_view_base.ipp#L263

> Username: alandefreitas  
> Created_at: 2022-03-22 18:54:25 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r832521303  

Oh... OK. I attempted to replicate the logic in `parser::parse_fields`.

> Username: alandefreitas  
> Created_at: 2022-03-22 19:46:44 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r832563767  

I'm not sure I know what to do here.   
  
1) Should we keep `build_tab` and make it call some kind of `write_table` functions somewhere else? Where? What would `buf_len_` represent in that case?  
2) Or should we just reuse the logic from `write_table` in `build_tab`? If so, I'm assuming the equivalent of `fields_view_base::buf_len_` would be `url::cap_`, but the initial condition `buf_len_ > 0` (or `cap_ > 0`) will always be true, because we allocate before writing the table in the `url`.

> Username: vinniefalco  
> Created_at: 2022-03-22 21:03:02 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r832623556  

What I'm saying is that having the function `url_view::write_table( void* dest )` seems like it would be a good primitive, based on experience in HTTP fields. However, unlike fields we have two tables. Yes `buf_len_` in fields is the same as `cap_` in URL (maybe I will rename it heh).  
  
I will leave the decision on implementation interface up to you.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 05:59:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/149#pullrequestreview-916694733  

📁 include/boost/url/impl/url.ipp

```diff
2277 |+     BOOST_ASSERT(s_);
2278 |+ 
2279 |+     if (nseg_ != 0)
```

> Username: vinniefalco  
> Created_at: 2022-03-22 05:59:45 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r831795577  

we already know the beginning of the first segment (`offset(id_path)`) and the end of the last segment (`offset(id_query)`). So the table doesn't need to store these.

> Username: alandefreitas  
> Created_at: 2022-03-22 20:05:29 UTC  
> Updated_at: 2022-03-22 20:05:30 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r832578560  

Yes. I saw that logic for fields.   
  
Are they also always at the beginning for segments and query parameters?   
  
I thought these views (segments and params) implemented more complex logic to get rid of edge case prefixes and suffixes so I thought it wasn't worth reimplementing this logic elsewhere.

> Username: vinniefalco  
> Created_at: 2022-03-22 21:01:21 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r832622302  

I don't think there are any edge cases, but I could be wrong. Its up to you.

> Username: alandefreitas  
> Created_at: 2022-03-22 22:34:36 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r832683005  

There are some considerations that make it a little more complex than fields because the first element is not at index `0`.  
  
Like  
  
https://github.com/CPPAlliance/url/blob/fbc51ed52412d1421767effa92951ff417c1691f/include/boost/url/detail/path.hpp#L23  
  
and  
  
https://github.com/CPPAlliance/url/blob/fbc51ed52412d1421767effa92951ff417c1691f/include/boost/url/impl/url_view.ipp#L402  
  
This logic is not complex. It's just more paths to cover because inferring the first `n` elements requires us to replicate this logic elsewhere to reconstruct these views to find the element.  
  
But I'll start with the simpler logic and adapt on top of that.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 06:01:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/149#pullrequestreview-916695823  

📁 include/boost/url/impl/url.ipp

```diff
2369 |+         sizeof(detail::params_table_entry);
2370 |+     new_cap = AP * ((new_cap + AP - 1) / AP) +
2371 |+            (nparam * SP);
```

> Username: vinniefalco  
> Created_at: 2022-03-22 06:01:27 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r831796410  

The problem here is we have two tables back to back. That means that any changes in size of the second table are going to cause memmove of the entire first table. I was thinking instead that we have just one table with enough space for the larger of the two sizes, where each element stores a param and a segment offset.

> Username: alandefreitas  
> Created_at: 2022-03-22 20:38:48 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r832605707  

> I was thinking instead that we have just one table with enough space for the larger of the two sizes, where each element stores a param and a segment offset.  
  
Mmm... This is possible. In that case, we would need a new name for the `<component>_table_entry`, `<component>_table`, and `const_<component>_table`.   
  
It's also a trade-off but I'm not sure of all the implications:  
  
- From a perspective of memory, we are obviously always spending more memory, unless `nseg == nparam`, which should be quite rare.  
- From a perspective of computational cost, I have the impression the merged table is absorbing the cost of manually moving elements into any operation changing any of the tables, which is more costly than memmoving only one of the tables.  
- From the perspective of code complexity, I'm not sure yet, but I have the impression we're going to have lots of ifs and elses to do what memmove would.

> Username: vinniefalco  
> Created_at: 2022-03-22 21:00:15 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r832621522  

Well I will leave the decision up to you


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 06:02:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/149#pullrequestreview-916696219  

📁 include/boost/url/impl/url.ipp

```diff
2391 |+         auto sp = s_ + old_cap;
2392 |+         auto st = detail::segments_table(sp);
2393 |+         st.copy(s + cap_, nseg_);
```

> Username: vinniefalco  
> Created_at: 2022-03-22 06:02:04 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r831796750  

well you are adding `old_cap` but then using `cap_` why not just leave out the local variable copy and just use `cap_`?

> Username: alandefreitas  
> Created_at: 2022-03-22 20:47:55 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r832612684  

Nice. Yes, it's possible to avoid the `old_cap` if we get the references to the tables first.  
  
Before changing this logic, I stored the `old_cap` before because `s = allocate(new_cap);` would invalidate the old `cap_`.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 23:24:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/149#pullrequestreview-918004275  

📁 include/boost/url/detail/config.hpp

```diff
  90 |+ namespace urls {
  91 |+ 
  92 |+ using off_t = ::uint16_t; // private
```

> Username: vinniefalco  
> Created_at: 2022-03-22 23:24:07 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r832708412  

what's wrong with unsigned short?

> Username: alandefreitas  
> Created_at: 2022-03-23 14:53:52 UTC  
> Updated_at: 2022-03-23 14:53:53 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r833364987  

I was hoping you would comment on that. I was going to use `size_t` or `uint32_t` directly on the table class, then I got this pattern from http_proto. What I don't get is why `uint16_t` is OK for fields. Why is it?

> Username: vinniefalco  
> Created_at: 2022-03-23 15:05:59 UTC  
> Updated_at: 2022-03-23 15:06:00 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r833378918  

don't you think 64kb is enough for an HTTP header?

> Username: alandefreitas  
> Created_at: 2022-03-23 15:11:12 UTC  
> Updated_at: 2022-03-24 14:31:41 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r833384857  

For the median HTTP header, yes. For the worst-case HTTP header according to the spec, I don't know.  
  
For the median URL, yes. For the worst-case URL according to the spec, no because of a [technicality](https://stackoverflow.com/questions/417142/what-is-the-maximum-length-of-a-url-in-different-browsers).


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 23:25:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/149#pullrequestreview-918005053  

📁 include/boost/url/detail/params_table.hpp

```diff
  13 |+ 
  14 |+ #include <boost/url/string_view.hpp>
  15 |+ #include <boost/assert.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-03-22 23:25:31 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r832709727  

do you need this include?


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 23:26:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/149#pullrequestreview-918005310  

📁 include/boost/url/detail/params_table.hpp

```diff
  31 |+     off_t kp;   // key pos
  32 |+     off_t kn;   // key size
  33 |+     off_t vp;   // value pos
```

> Username: vinniefalco  
> Created_at: 2022-03-22 23:26:03 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r832710201  

well if you know the key size then you also know the value pos, right? and if you know the position of the next key then that gives you the value length?


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-24 15:42:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/149#pullrequestreview-920479988  

📁 include/boost/url/detail/segments_table.hpp

```diff
  28 |+ struct segments_table_entry
  29 |+ {
  30 |+     std::uint32_t sp;   // segment pos
```

> Username: vinniefalco  
> Created_at: 2022-03-24 15:42:13 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r834454144  

why do you keep changing this type, it needs to be `off_t`


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-24 15:42:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/149#pullrequestreview-920480792  

📁 include/boost/url/impl/url.ipp

```diff
1535 |+ 
1536 |+     std::size_t param_pos = start - s_;
1537 |+     std::size_t param_n = end - start;
```

> Username: vinniefalco  
> Created_at: 2022-03-24 15:42:43 UTC  
> Updated_at: 2022-03-24 15:42:59 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r834454720  

I would use auto or does this create a problem with signed/unsigned types?

> Username: alandefreitas  
> Created_at: 2022-03-24 19:12:12 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r834641863  

`auto param_pos = start - s_;` would become `long param_pos = = start - s_;` and fail in the `raw_param` constructor.  
  
```  
Non-constant-expression cannot be narrowed from type 'long' to 'std::size_t' (aka 'unsigned long') in initializer list  
```


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-24 15:44:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/149#pullrequestreview-920484028  

📁 include/boost/url/detail/segments_table.hpp

```diff
  51 |+         return p_[-1 * (
  52 |+             static_cast<
  53 |+                 long>(i) + 1)];
```

> Username: vinniefalco  
> Created_at: 2022-03-24 15:44:57 UTC  
> Url: https://github.com/boostorg/url/pull/149#discussion_r834457012  

converting size_t to long is narrowing when size_t is 64-bits


---

## Comment 13

> Username: alandefreitas  
> Created_at: 2022-03-25 01:51:37 UTC  
> Updated_at: 2022-03-25 01:51:55 UTC  
> Url: https://github.com/boostorg/url/pull/149#issuecomment-1078575877  

The problem with segment prefixes and suffixes has been fixed.   
  
To do list:  
  
- Improve coverage  
- Add documentation about the malleable prefixes and suffixes  
- Add a few relevant new edge cases to the tests

---

## Comment 14

> Username: alandefreitas  
> Created_at: 2022-03-25 16:16:20 UTC  
> Url: https://github.com/boostorg/url/pull/149#issuecomment-1079183792  

Closing this PR as there is not enough user demand at this point.

---
