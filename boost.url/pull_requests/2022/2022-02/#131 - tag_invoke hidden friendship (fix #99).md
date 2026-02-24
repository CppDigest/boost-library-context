# #131 tag_invoke hidden friendship (fix #99) [Closed]

> Username: alandefreitas  
> Created at: 2022-02-24 19:24:40 UTC  
> Updated at: 2022-03-14 15:56:18 UTC  
> Closed at: 2022-02-25 00:46:41 UTC  
> Url: https://github.com/boostorg/url/pull/131  

This PR defines the befriended `tag_invoke` functions fully within the class synopsis so that they are hidden friends accessible through ADL only.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-02-24 19:29:46 UTC  
> Url: https://github.com/boostorg/url/pull/131#issuecomment-1050190553  

An automated preview of the documentation is available at [https://131.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://131.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-02-24 23:54:40 UTC  
> Url: https://github.com/boostorg/url/pull/131#issuecomment-1050373336  

An automated preview of the documentation is available at [https://131.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://131.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-25 00:33:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/131#pullrequestreview-893153551  

📁 include/boost/url/ipv4_address.hpp

```diff
 263 |     /** Customization point for parsing an IPv4 address.
 264 |     */
 265 |     BOOST_URL_DECL
```

> Username: vinniefalco  
> Created_at: 2022-02-25 00:33:19 UTC  
> Url: https://github.com/boostorg/url/pull/131#discussion_r814384268  

This should not be exported should it?


---
