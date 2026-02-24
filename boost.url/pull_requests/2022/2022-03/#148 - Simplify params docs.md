# #148 Simplify params docs [Merged]

> Username: alandefreitas  
> Created at: 2022-03-21 19:39:00 UTC  
> Updated at: 2022-06-08 13:37:56 UTC  
> Merged at: 2022-03-22 19:59:59 UTC  
> Closed at: 2022-03-22 19:59:59 UTC  
> Url: https://github.com/boostorg/url/pull/148  

fix #145

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-03-21 19:44:44 UTC  
> Url: https://github.com/boostorg/url/pull/148#issuecomment-1074343888  

An automated preview of the documentation is available at [https://148.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://148.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-03-21 22:49:27 UTC  
> Updated_at: 2022-03-22 18:00:48 UTC  
> Url: https://github.com/boostorg/url/pull/148#issuecomment-1074497005  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/148?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#148](https://codecov.io/gh/CPPAlliance/url/pull/148?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (c346427) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/3f9954e5596d7cf75c23e3a95eb00f6864c660ae?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (3f9954e) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head c346427 differs from pull request most recent head fbc51ed. Consider uploading reports for the commit fbc51ed to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/148/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/148?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #148   +/-   ##  
========================================  
  Coverage    96.80%   96.80%             
========================================  
  Files          110      110             
  Lines         6197     6197             
========================================  
  Hits          5999     5999             
  Misses         198      198             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/148?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/params.hpp](https://codecov.io/gh/CPPAlliance/url/pull/148/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_view.hpp](https://codecov.io/gh/CPPAlliance/url/pull/148/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/148?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/148?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [3f9954e...fbc51ed](https://codecov.io/gh/CPPAlliance/url/pull/148?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 15:05:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/148#pullrequestreview-917420373  

📁 include/boost/url/params.hpp

```diff
  42 |-     /** A pointer to the url that contains the query parameters
  43 |-      */
  44 |     url* u_ = nullptr;
```

> Username: vinniefalco  
> Created_at: 2022-03-22 15:05:37 UTC  
> Updated_at: 2022-03-22 15:05:39 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832305821  

lol... possibly the most useless comment ever


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 15:06:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/148#pullrequestreview-917421402  

📁 include/boost/url/params.hpp

```diff
 106 |+             This string holds the query value
 107 |+             with percent-decoding applied.
 108 |+ 
```

> Username: vinniefalco  
> Created_at: 2022-03-22 15:06:15 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832306532  

Do note that newlines are ignored by doxygen. so this will appear as one paragraph.

> Username: alandefreitas  
> Created_at: 2022-03-22 15:38:20 UTC  
> Updated_at: 2022-03-22 15:38:21 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832346560  

Yes. I think it only creates paragraphs with `<br>`. But I do like [the way this is rendered](https://148.url.prtest.cppalliance.org/libs/url/doc/html/url/ref/boost__urls__params__reference/value.html). Would you like to change that?

> Username: vinniefalco  
> Created_at: 2022-03-22 17:13:07 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832427441  

Its fine as-is, just pointing out that if you were expecting a line break, it isn't happening :)


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 15:07:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/148#pullrequestreview-917424424  

📁 include/boost/url/params.hpp

```diff
 147 | 
 179 |-         Each instance of @ref params::value_type is a view of a query <key, value> pair.
 148 |+         Assignment from initializer list.
```

> Username: vinniefalco  
> Created_at: 2022-03-22 15:07:53 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832308550  

Well this is not a sentence, since there is no verb. How about "The query parameters are replaced by the contents of the initializer list. The behavior is undefined if any strings in the list reference part or all of the URL."

> Username: alandefreitas  
> Created_at: 2022-03-22 15:51:10 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832357509  

OK. I was trying to reduce things in this PR but I think I went too far. :)


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 15:08:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/148#pullrequestreview-917425550  

📁 include/boost/url/params.hpp

```diff
 199 |     void
 200 |     assign(FwdIt first, FwdIt last,
 201 |         std::input_iterator_tag) = delete;
```

> Username: vinniefalco  
> Created_at: 2022-03-22 15:08:33 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832309334  

\sigh.. please open an issue in Docca asking for this to be fixed? Is it a Doxygen issue? The more info the better.

> Username: alandefreitas  
> Created_at: 2022-03-22 17:34:07 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832447853  

Is this "function" supposed to be public though? Shouldn't it be private along with `assign(FwdIt first, FwdIt last, std::forward_iterator_tag)`? It's only used indirectly by `assign(FwdIt first, FwdIt last)`.

> Username: alandefreitas  
> Created_at: 2022-03-22 17:40:40 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832453640  

Anyway, I generated the documentation without the guards, and this is what we get:  
  
![image](https://user-images.githubusercontent.com/5369819/159541506-6d140f7d-8d8a-4e9d-8a72-63dcaf9fb7a1.png)  
  
![image](https://user-images.githubusercontent.com/5369819/159541575-385f8260-c217-4f09-ad30-2b3fa55ff8d0.png)  
  
So I assume this is a docca issue. I opened the issue [here](https://github.com/boostorg/docca/issues/122).


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 15:09:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/148#pullrequestreview-917426999  

📁 include/boost/url/params.hpp

```diff
 255 |- 
 256 |-     /** Access element at specified position with bounds checking
 204 |+     /** Access specified element with bounds checking
```

> Username: vinniefalco  
> Created_at: 2022-03-22 15:09:27 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832310403  

Functions which return values almost always have briefs that start with the word "Return"  
  
"Return the indexed element, with bounds checking."


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 15:10:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/148#pullrequestreview-917431177  

📁 include/boost/url/params.hpp

```diff
 246 | 
 297 |-     /** Access last query parameter in the container
 247 |+     /** Access the last element
```

> Username: vinniefalco  
> Created_at: 2022-03-22 15:10:22 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832311704  

Yeah my fault for using the word Access, I just copied cppreference :)

> Username: alandefreitas  
> Created_at: 2022-03-22 17:42:27 UTC  
> Updated_at: 2022-03-22 17:42:28 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832455243  

I changed it to "Return". But yes. cppreference uses the word "access". I actually like this word because it's a short way to also imply this is a reference.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 15:11:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/148#pullrequestreview-917433076  

📁 include/boost/url/params.hpp

```diff
 292 |     /** Clears the contents of the container
 293 | 
 294 |         Clears the contents of the container as if calling
```

> Username: vinniefalco  
> Created_at: 2022-03-22 15:11:06 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832312749  

It looks bad when the description starts with the same word or words as the brief.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 15:11:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/148#pullrequestreview-917434159  

📁 include/boost/url/params.hpp

```diff
 303 |+         Insert element at the specified container position
 304 |+ 
 305 |+         Behavior is undefined if the element belongs to the container
```

> Username: vinniefalco  
> Created_at: 2022-03-22 15:11:44 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832313561  

It looks bad when the description starts with the same word or words as the brief. And "Behavior is undefined" needs a definite article ("the").

> Username: vinniefalco  
> Created_at: 2022-03-22 15:13:39 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832315889  

No one is going to understand "if the element belongs to the container." It needs to be specific, e.g. "the key and value strings must not reference the underlying URL buffer, or else the behavior is undefined."  
  
However, in this case the behavior is not undefined - don't we use copied strings for single value inserts?


---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-03-22 17:54:48 UTC  
> Url: https://github.com/boostorg/url/pull/148#issuecomment-1075447690  

An automated preview of the documentation is available at [https://148.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://148.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-03-22 19:51:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/148#pullrequestreview-917801506  

📁 include/boost/url/params.hpp

```diff
  67 | 
  68 |+         An instance of this type allows for making
  69 |+         a copy of a query parameter where ownership
```

> Username: vinniefalco  
> Created_at: 2022-03-22 19:51:09 UTC  
> Updated_at: 2022-03-22 19:51:10 UTC  
> Url: https://github.com/boostorg/url/pull/148#discussion_r832567245  

allows copied to be made of


---
