# #425 Relative path [Closed]

> Username: alandefreitas  
> Created at: 2022-08-18 16:05:56 UTC  
> Updated at: 2022-12-22 14:58:05 UTC  
> Closed at: 2022-12-22 14:58:04 UTC  
> Url: https://github.com/boostorg/url/pull/425  

fix #407

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-08-18 16:19:15 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1219687926  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-18 16:35:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/425#pullrequestreview-1077678569  

📁 include/boost/url/authority_view.hpp

```diff
1121 |+         @par Effects
1122 |+         @code
1123 |+         return url( a0 ).normalize() <= url( a1 ).normalize();
```

> Username: vinniefalco  
> Created_at: 2022-08-18 16:35:58 UTC  
> Updated_at: 2022-08-18 16:35:59 UTC  
> Url: https://github.com/boostorg/url/pull/425#discussion_r949369489  

`url`?


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-18 16:37:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/425#pullrequestreview-1077679958  

📁 include/boost/url/impl/url.ipp

```diff
 170 |+     {
 171 |+         // cannot calculate a URI relative to another relative URI
 172 |+         return error::not_a_base;
```

> Username: vinniefalco  
> Created_at: 2022-08-18 16:37:07 UTC  
> Updated_at: 2022-08-18 16:37:08 UTC  
> Url: https://github.com/boostorg/url/pull/425#discussion_r949370518  

line number / file?


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-18 16:37:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/425#pullrequestreview-1077680271  

📁 include/boost/url/impl/url.ipp

```diff
 175 |+     // Resolve scheme
 176 |+     if (href.scheme() == base.scheme() ||
 177 |+         !href.has_scheme())
```

> Username: vinniefalco  
> Created_at: 2022-08-18 16:37:22 UTC  
> Updated_at: 2022-08-18 16:37:23 UTC  
> Url: https://github.com/boostorg/url/pull/425#discussion_r949370719  

indent this line please so it is not confused for the body statement


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-18 16:38:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/425#pullrequestreview-1077681674  

📁 include/boost/url/impl/url_view_base.ipp

```diff
 555 |+     if (has_scheme())
 556 |+     {
 557 |+         comp = detail::ci_compare(
```

> Username: vinniefalco  
> Created_at: 2022-08-18 16:38:30 UTC  
> Updated_at: 2022-08-18 16:38:31 UTC  
> Url: https://github.com/boostorg/url/pull/425#discussion_r949371719  

we need this as a public function in `grammar` if it is a case-insensitive comparison

> Username: alandefreitas  
> Created_at: 2022-08-18 20:41:39 UTC  
> Url: https://github.com/boostorg/url/pull/425#discussion_r949581332  

Where? What file?


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-18 16:38:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/425#pullrequestreview-1077682108  

📁 include/boost/url/impl/url_view_base.ipp

```diff
 568 |+     if (has_authority())
 569 |+     {
 570 |+         comp = authority().compare(other.authority());
```

> Username: vinniefalco  
> Created_at: 2022-08-18 16:38:51 UTC  
> Updated_at: 2022-08-18 16:38:52 UTC  
> Url: https://github.com/boostorg/url/pull/425#discussion_r949372053  

Does `authority()` re-parse?

> Username: alandefreitas  
> Created_at: 2022-08-18 20:42:22 UTC  
> Url: https://github.com/boostorg/url/pull/425#discussion_r949581807  

No no.


---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-08-18 16:39:54 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1219706538  

quite a lot here !

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2022-08-18 17:27:23 UTC  
> Updated_at: 2022-08-26 22:18:03 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1219748776  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/425?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#425](https://codecov.io/gh/CPPAlliance/url/pull/425?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (579deee) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/afb91bc384168ed912e991ae436b8e8bbbde7d89?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (afb91bc) will **decrease** coverage by `0.07%`.  
> The diff coverage is `98.75%`.  
  
> :exclamation: Current head 579deee differs from pull request most recent head f9c5121. Consider uploading reports for the commit f9c5121 to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/425/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/425?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #425      +/-   ##  
===========================================  
- Coverage    97.92%   97.84%   -0.08%       
===========================================  
  Files          132      132                
  Lines         6349     6494     +145       
===========================================  
+ Hits          6217     6354     +137       
- Misses         132      140       +8       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/425?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url.hpp](https://codecov.io/gh/CPPAlliance/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/url.ipp](https://codecov.io/gh/CPPAlliance/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmwuaXBw) | `97.04% <98.11%> (+1.80%)` | :arrow_up: |  
| [include/boost/url/authority\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `94.87% <100.00%> (+0.58%)` | :arrow_up: |  
| [include/boost/url/impl/authority\_view.ipp](https://codecov.io/gh/CPPAlliance/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9hdXRob3JpdHlfdmlldy5pcHA=) | `96.77% <100.00%> (+2.03%)` | :arrow_up: |  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/CPPAlliance/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `98.87% <100.00%> (ø)` | |  
| [include/boost/url/impl/url\_view\_base.ipp](https://codecov.io/gh/CPPAlliance/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlld19iYXNlLmlwcA==) | `98.56% <100.00%> (-1.44%)` | :arrow_down: |  
| [include/boost/url/url\_view\_base.hpp](https://codecov.io/gh/CPPAlliance/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/impl/normalize.ipp](https://codecov.io/gh/CPPAlliance/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvbm9ybWFsaXplLmlwcA==) | `97.46% <0.00%> (-1.90%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/425?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/425?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [afb91bc...f9c5121](https://codecov.io/gh/CPPAlliance/url/pull/425?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-08-18 20:50:29 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1219948419  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 10

> Username: alandefreitas  
> Created_at: 2022-08-18 21:06:14 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1219963138  

> quite a lot here !  
  
Yes. This function seems useful. The PR was also useful to find lots of small bugs and features missing here and there. Like some problems with the `url_base::ops` and `set_path("")`. It also gave me some ideas to improve that noexcept path comparison we have in `url_view::compare`.   
  
Some other questions that came up:  
  
- About the order of arguments: `std::filesystem`/`boost::filesystem` functions use `relative(const path&, const path& base)`. Also `absolute`, `proximate`, etc. It does look more natural. Shouldn't we do the same here to `relative`/`absolute`/`resolve`?  
  
- The basic exception guarantee is not satisfied in case of allocation errors after the first allocation. Is that a problem?

---

## Comment 11

> Username: alandefreitas  
> Created_at: 2022-08-18 21:45:33 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1219998131  

Oh... look what URI.js says about `absoluteTo`:  
  
https://github.com/medialize/URI.js/blob/28976cde5bb32a433ab78c4a32ef58f39d2cabf6/test/test.js#L1479  
  
So we only need `resolve` and `relative`.

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-08-18 23:50:27 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1220087023  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-08-19 20:55:30 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1221085810  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 14

> Username: alandefreitas  
> Created_at: 2022-08-19 21:00:33 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1221088970  

I fixed the last test that was failing:  
  
```cpp  
check("/../path/x/../to/y/../somewhere/else", "/a/../../path/to/a", "../a");  
```  
  
This function had a problem similar to `op==`: comparing paths without a stack. The difference is we need the longest common path instead.  
  
As usual, I tried many solutions and realized comparing the segments in reverse is the only solution with linear cost. Because of how hard this is and so we can move on, I used the other solution which could be n^2 in a very edgy case.

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2022-08-19 21:04:19 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1221091211  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2022-08-19 21:09:38 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1221094217  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2022-08-24 19:15:29 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1226136228  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 18

> Username: vinniefalco  
> Created_at: 2022-08-25 14:12:18 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1227318488  

we can use temporary memory with `recycled_ptr` now if it will simplify and speed up the algorithm

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2022-08-26 19:45:24 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1228856173  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2022-08-26 21:30:25 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1228964745  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2022-08-26 22:25:25 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1229011032  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-26 22:39:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/425#pullrequestreview-1087571576  

📁 include/boost/url/impl/url.ipp

```diff
 332 |-     return {};
 148 |+     dest.copy(base);
 149 |+     return dest.relative(href);
```

> Username: vinniefalco  
> Created_at: 2022-08-26 22:39:29 UTC  
> Url: https://github.com/boostorg/url/pull/425#discussion_r956483959  

`relative` is the name? That is kind of obscure... doesn't really suggest what it does. Why are we adding features

> Username: alandefreitas  
> Created_at: 2022-08-26 23:12:05 UTC  
> Url: https://github.com/boostorg/url/pull/425#discussion_r956492920  

> relative is the name? That is kind of obscure... doesn't really suggest what it does.   
  
URL.js uses `relative_to`. I can't think of anything better than `relative`/`relative_to`  
  
> Why are we adding features  
  
I don't know. I was assigned this issue and started writing the code at some point.


---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2022-09-07 21:57:11 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1239946474  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2022-09-09 19:28:10 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1242379974  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2022-09-14 22:06:45 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1247348675  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2022-09-14 22:16:47 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1247355795  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 27

> Username: alandefreitas  
> Created_at: 2022-09-14 22:20:54 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1247358853  

I rebased this PR and fixed everything that broke with the new containers.

---

## Comment 28

> Username: codecov[bot]  
> Created_at: 2022-09-14 22:23:10 UTC  
> Updated_at: 2022-10-05 16:24:43 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1247360652  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/425?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#425](https://codecov.io/gh/boostorg/url/pull/425?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (559dc25) into [develop](https://codecov.io/gh/boostorg/url/commit/ecdc8f1a5e6685c698a06be920e2ba3effb3c43a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ecdc8f1) will **increase** coverage by `0.01%`.  
> The diff coverage is `98.88%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/425/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/425?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #425      +/-   ##  
===========================================  
+ Coverage    96.81%   96.83%   +0.01%       
===========================================  
  Files          139      139                
  Lines         6691     6847     +156       
===========================================  
+ Hits          6478     6630     +152       
- Misses         213      217       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/425?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url.hpp](https://codecov.io/gh/boostorg/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `98.95% <98.19%> (-0.08%)` | :arrow_down: |  
| [include/boost/url/authority\_view.hpp](https://codecov.io/gh/boostorg/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `94.59% <100.00%> (+0.65%)` | :arrow_up: |  
| [include/boost/url/impl/authority\_view.ipp](https://codecov.io/gh/boostorg/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9hdXRob3JpdHlfdmlldy5pcHA=) | `84.17% <100.00%> (+5.31%)` | :arrow_up: |  
| [include/boost/url/impl/url\_view\_base.ipp](https://codecov.io/gh/boostorg/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlld19iYXNlLmlwcA==) | `98.60% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/url/url\_base.hpp](https://codecov.io/gh/boostorg/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/detail/impl/normalize.ipp](https://codecov.io/gh/boostorg/url/pull/425/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvbm9ybWFsaXplLmlwcA==) | `97.46% <0.00%> (-1.90%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/425?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/425?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ecdc8f1...559dc25](https://codecov.io/gh/boostorg/url/pull/425?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 29

> Username: cppalliance-bot  
> Created_at: 2022-10-05 15:37:33 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1268605991  

GCOVR code coverage report [https://425.url.prtest.cppalliance.org/gcovr/index.html](https://425.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://425.url.prtest.cppalliance.org/genhtml/index.html](https://425.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 30

> Username: cppalliance-bot  
> Created_at: 2022-10-05 15:38:29 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1268606912  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 31

> Username: cppalliance-bot  
> Created_at: 2022-10-12 23:33:29 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1276844789  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 32

> Username: cppalliance-bot  
> Created_at: 2022-10-12 23:46:29 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1276851808  

GCOVR code coverage report [https://425.url.prtest.cppalliance.org/gcovr/index.html](https://425.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://425.url.prtest.cppalliance.org/genhtml/index.html](https://425.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 33

> Username: cppalliance-bot  
> Created_at: 2022-10-19 23:48:48 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1284711211  

An automated preview of the documentation is available at [https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://425.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 34

> Username: cppalliance-bot  
> Created_at: 2022-10-19 23:53:31 UTC  
> Url: https://github.com/boostorg/url/pull/425#issuecomment-1284714138  

GCOVR code coverage report [https://425.url.prtest.cppalliance.org/gcovr/index.html](https://425.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://425.url.prtest.cppalliance.org/genhtml/index.html](https://425.url.prtest.cppalliance.org/genhtml/index.html)

---
