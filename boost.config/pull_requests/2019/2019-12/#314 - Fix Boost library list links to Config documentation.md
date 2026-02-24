# #314 Fix Boost library list links to Config documentation [Merged]

> Username: glenfe  
> Created at: 2019-12-24 12:54:42 UTC  
> Updated at: 2019-12-24 18:14:45 UTC  
> Merged at: 2019-12-24 18:14:45 UTC  
> Closed at: 2019-12-24 18:14:45 UTC  
> Url: https://github.com/boostorg/config/pull/314  



---

## Comment 1

> Username: eldiener  
> Created_at: 2019-12-24 16:11:33 UTC  
> Url: https://github.com/boostorg/config/pull/314#issuecomment-568772838  

Shouldn't this just be "index.html" rather than "doc/html/index.html" ?

---

## Comment 2

> Username: glenfe  
> Created_at: 2019-12-24 16:15:24 UTC  
> Url: https://github.com/boostorg/config/pull/314#issuecomment-568773368  

Going directly to "doc/html/index.html" saves a redirect. Ideally more of the meta/libraries.json would do this, such that the generated libraries.htm pages have fewer redirects.   
  
e.g. https://www.boost.org/doc/libs/master/libs/libraries.htm#IO    The link to Boost.IO avoids the redirecting index.html and goes directly to IO's documentation.

---

## Comment 3

> Username: eldiener  
> Created_at: 2019-12-24 17:58:38 UTC  
> Url: https://github.com/boostorg/config/pull/314#issuecomment-568785532  

All other libraries in a Boost distribution have the link to their docs in the top level directory via an index.html. There is no reason config should be different.

---

## Comment 4

> Username: glenfe  
> Created_at: 2019-12-24 18:01:04 UTC  
> Url: https://github.com/boostorg/config/pull/314#issuecomment-568785822  

Config is no different; it has an index.html which has that same kind of redirect. But there are Boost libraries which specify "documentation" in meta/libraries.json so that all relevant links can bypass that index.html redirect. i.e. Not *all* Boost Libraries avoid the documentation: element in meta/libraries.json

---

## Comment 5

> Username: glenfe  
> Created_at: 2019-12-24 18:06:55 UTC  
> Url: https://github.com/boostorg/config/pull/314#issuecomment-568786483  

Here are some of the Boost libraries that specify a documentation tag in meta/libraries.json directly to the documentation (avoiding redirect HTML files):  
https://github.com/search?utf8=%E2%9C%93&q=org%3Aboostorg+filename%3Alibraries.json+documentation&type=Code  
  
- Tribool  
- Interval  
- IO  
- Fusion  
- DynamicBitset  
- Exception  
  
To list a few. (i.e. Not quite "All other").

---
