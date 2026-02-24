# #288 Add recycled_ptr and optimize ranges [Closed]

> Username: vinniefalco  
> Created at: 2022-08-01 23:05:14 UTC  
> Updated at: 2022-08-02 17:43:42 UTC  
> Closed at: 2022-08-02 04:34:54 UTC  
> Url: https://github.com/boostorg/url/pull/288  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-08-01 23:24:45 UTC  
> Url: https://github.com/boostorg/url/pull/288#issuecomment-1201834728  

An automated preview of the documentation is available at [https://288.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://288.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-08-01 23:49:45 UTC  
> Url: https://github.com/boostorg/url/pull/288#issuecomment-1201849508  

An automated preview of the documentation is available at [https://288.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://288.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-08-02 00:09:44 UTC  
> Url: https://github.com/boostorg/url/pull/288#issuecomment-1201860736  

An automated preview of the documentation is available at [https://288.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://288.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-08-02 00:14:44 UTC  
> Url: https://github.com/boostorg/url/pull/288#issuecomment-1201863436  

An automated preview of the documentation is available at [https://288.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://288.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-08-02 00:34:44 UTC  
> Url: https://github.com/boostorg/url/pull/288#issuecomment-1201873650  

An automated preview of the documentation is available at [https://288.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://288.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 6 [Commented]

> Username: alandefreitas  
> Created_at: 2022-08-02 00:35:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/288#pullrequestreview-1057986929  

Looks good

📁 include/boost/url/detail/impl/recycler.ipp

```diff
  24 |+ namespace detail {
  25 |+ 
  26 |+ struct all_reports
```

> Username: alandefreitas  
> Created_at: 2022-08-02 00:30:04 UTC  
> Updated_at: 2022-08-02 00:35:27 UTC  
> Url: https://github.com/boostorg/url/pull/288#discussion_r935013925  

Oh... I thought that was just for tests.

> Username: vinniefalco  
> Created_at: 2022-08-02 00:37:17 UTC  
> Updated_at: 2022-08-02 00:37:18 UTC  
> Url: https://github.com/boostorg/url/pull/288#discussion_r935016318  

These reports are for real programs. I wrote it this way to make sure that everything compiles, even if the reports are turned off (you have to define `BOOST_URL_REPORTS` to get results).


📁 include/boost/url/grammar/impl/range_rule.hpp

```diff
 202 | //------------------------------------------------
 203 | 
 204 |+ // small
```

> Username: alandefreitas  
> Created_at: 2022-08-02 00:32:20 UTC  
> Updated_at: 2022-08-02 00:35:28 UTC  
> Url: https://github.com/boostorg/url/pull/288#discussion_r935014695  

Small and big... nice


📁 include/boost/url/rfc/detail/hier_part_rule.hpp

```diff
  43 |-         // VFALCO This doesn't belong here
  44 |-         grammar::range<pct_encoded_view> path;
  42 |+         grammar::range<
```

> Username: alandefreitas  
> Created_at: 2022-08-02 00:34:48 UTC  
> Updated_at: 2022-08-02 00:35:28 UTC  
> Url: https://github.com/boostorg/url/pull/288#discussion_r935015550  

Are we going to change this to `segments` in the future?

> Username: vinniefalco  
> Created_at: 2022-08-02 00:37:50 UTC  
> Url: https://github.com/boostorg/url/pull/288#discussion_r935016505  

I don't know, because this is not user-facing. So there is no real incentive to change it right before the review. We could in theory tidy it up later.


---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-08-02 04:34:54 UTC  
> Url: https://github.com/boostorg/url/pull/288#issuecomment-1202006231  

Merged

---
