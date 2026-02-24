# #561 self-assignment is a no-op [Closed]

> Username: alandefreitas  
> Created at: 2022-09-19 17:38:27 UTC  
> Updated at: 2022-09-19 21:24:37 UTC  
> Closed at: 2022-09-19 21:20:43 UTC  
> Url: https://github.com/boostorg/url/pull/561  

fix #556

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-19 17:39:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/561#pullrequestreview-1112635132  

📁 include/boost/url/impl/params_encoded_ref.ipp

```diff
  42 | {
  43 |-     assign(other.begin(), other.end());
  43 |+     if (this != &other)
```

> Username: vinniefalco  
> Created_at: 2022-09-19 17:39:20 UTC  
> Updated_at: 2022-09-19 17:44:45 UTC  
> Url: https://github.com/boostorg/url/pull/561#discussion_r974512970  

That's not enough you need to check `u_ != other.u_`


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-19 17:43:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/561#pullrequestreview-1112640442  

📁 include/boost/url/static_url.hpp

```diff
 291 |     {
 292 |-         copy(u);
 292 |+         if (this != &u)
```

> Username: vinniefalco  
> Created_at: 2022-09-19 17:43:22 UTC  
> Url: https://github.com/boostorg/url/pull/561#discussion_r974516355  

This is good


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-19 17:44:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/561#pullrequestreview-1112641461  

📁 include/boost/url/impl/segments_ref.ipp

```diff
  47 | {
  48 |-     assign(other.begin(), other.end());
  48 |+     if (this != &other)
```

> Username: vinniefalco  
> Created_at: 2022-09-19 17:44:14 UTC  
> Url: https://github.com/boostorg/url/pull/561#discussion_r974517070  

This needs to check `u_ != other.u_`


---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2022-09-19 18:31:00 UTC  
> Updated_at: 2022-09-19 20:12:26 UTC  
> Url: https://github.com/boostorg/url/pull/561#issuecomment-1251393821  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/561?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#561](https://codecov.io/gh/boostorg/url/pull/561?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f9ce420) into [develop](https://codecov.io/gh/boostorg/url/commit/9be1f3e2a8c8847cb3351f7b798da73e9e62c464?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9be1f3e) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/561/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/561?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #561   +/-   ##  
========================================  
  Coverage    96.78%   96.79%             
========================================  
  Files          138      138             
  Lines         6760     6769    +9       
========================================  
+ Hits          6543     6552    +9       
  Misses         217      217             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/561?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/static\_url.hpp](https://codecov.io/gh/boostorg/url/pull/561/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvc3RhdGljX3VybC5ocHA=) | `80.00% <ø> (ø)` | |  
| [include/boost/url/impl/params\_encoded\_ref.ipp](https://codecov.io/gh/boostorg/url/pull/561/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfZW5jb2RlZF9yZWYuaXBw) | `68.42% <100.00%> (+0.33%)` | :arrow_up: |  
| [include/boost/url/impl/params\_ref.ipp](https://codecov.io/gh/boostorg/url/pull/561/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9wYXJhbXNfcmVmLmlwcA==) | `67.39% <100.00%> (+0.35%)` | :arrow_up: |  
| [include/boost/url/impl/segments\_encoded\_ref.ipp](https://codecov.io/gh/boostorg/url/pull/561/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50c19lbmNvZGVkX3JlZi5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/segments\_ref.ipp](https://codecov.io/gh/boostorg/url/pull/561/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9zZWdtZW50c19yZWYuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/561/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `98.69% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/url/impl/url\_view.ipp](https://codecov.io/gh/boostorg/url/pull/561/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlldy5pcHA=) | `96.15% <100.00%> (+0.32%)` | :arrow_up: |  
| [include/boost/url/url\_view.hpp](https://codecov.io/gh/boostorg/url/pull/561/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/561?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/561?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9be1f3e...f9ce420](https://codecov.io/gh/boostorg/url/pull/561?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-19 20:41:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/561#pullrequestreview-1112855522  

📁 test/unit/url_view.cpp

```diff
  63 |+             url_view u2 = u1;
  64 |+             u2 = u1;
  65 |+             BOOST_TEST_EQ(u1.data(), u1.data());
```

> Username: vinniefalco  
> Created_at: 2022-09-19 20:41:04 UTC  
> Url: https://github.com/boostorg/url/pull/561#discussion_r974671579  

I thought I already did this test??

> Username: vinniefalco  
> Created_at: 2022-09-19 20:42:34 UTC  
> Url: https://github.com/boostorg/url/pull/561#discussion_r974672808  

if you are going to have more than one test per member please group them together under one comment, like this:  
```  
        // operator=(url_view const&)  
        {  
            url_view u1("x://y/z?#");  
            url_view u2;  
            u2 = u1;  
            BOOST_TEST_EQ(u2.data(), u1.data());  
        }  
        {  
            // self-assignment  
            url_view u1("x://y/z?#");  
            u1 = u1;  
            BOOST_TEST_EQ(u1.data(), u1.data());  
        }  
```  
  
and add a comment explaining what the difference is


---

## Comment 6

> Username: alandefreitas  
> Created_at: 2022-09-19 21:20:43 UTC  
> Url: https://github.com/boostorg/url/pull/561#issuecomment-1251573417  

merged

---
