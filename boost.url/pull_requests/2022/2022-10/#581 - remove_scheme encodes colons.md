# #581 remove_scheme encodes colons [Merged]

> Username: alandefreitas  
> Created at: 2022-10-05 18:33:08 UTC  
> Updated at: 2022-12-22 16:20:22 UTC  
> Merged at: 2022-10-19 23:38:44 UTC  
> Closed at: 2022-10-19 23:38:44 UTC  
> Url: https://github.com/boostorg/url/pull/581  

fix #382

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-05 18:43:14 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1268811187  

GCOVR code coverage report [https://581.url.prtest.cppalliance.org/gcovr/index.html](https://581.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://581.url.prtest.cppalliance.org/genhtml/index.html](https://581.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-10-05 18:50:03 UTC  
> Updated_at: 2022-10-19 21:28:56 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1268819611  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/581?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#581](https://codecov.io/gh/boostorg/url/pull/581?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b5b60b6) into [develop](https://codecov.io/gh/boostorg/url/commit/49f6c2f3d7b26ffc6cdbe35483940f6f7003138a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (49f6c2f) will **increase** coverage by `0.04%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/581/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/581?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #581      +/-   ##  
===========================================  
+ Coverage    96.70%   96.75%   +0.04%       
===========================================  
  Files          137      137                
  Lines         6626     6696      +70       
===========================================  
+ Hits          6408     6479      +71       
+ Misses         218      217       -1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/581?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url\_base.hpp](https://codecov.io/gh/boostorg/url/pull/581/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/any\_segments\_iter.hpp](https://codecov.io/gh/boostorg/url/pull/581/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9zZWdtZW50c19pdGVyLmhwcA==) | `98.30% <100.00%> (+0.12%)` | :arrow_up: |  
| [...nclude/boost/url/detail/impl/any\_segments\_iter.ipp](https://codecov.io/gh/boostorg/url/pull/581/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2ltcGwvYW55X3NlZ21lbnRzX2l0ZXIuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/581/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `99.16% <100.00%> (+0.13%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/581?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/581?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [49f6c2f...b5b60b6](https://codecov.io/gh/boostorg/url/pull/581?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-10-05 20:53:05 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1268963541  

GCOVR code coverage report [https://581.url.prtest.cppalliance.org/gcovr/index.html](https://581.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://581.url.prtest.cppalliance.org/genhtml/index.html](https://581.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-06 02:51:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/581#pullrequestreview-1132246447  

📁 include/boost/url/url_base.hpp

```diff
 227 |+     BOOST_URL_DECL
 228 |+     url_base&
 229 |+     remove_scheme();
```

> Username: vinniefalco  
> Created_at: 2022-10-06 02:51:29 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r988504949  

You need to make sure that these functions are in the same order as they are in the tip of develop

> Username: alandefreitas  
> Created_at: 2022-10-06 17:51:46 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r989331742  

OK


---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-10-06 02:53:24 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1269237807  

GCOVR code coverage report [https://581.url.prtest.cppalliance.org/gcovr/index.html](https://581.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://581.url.prtest.cppalliance.org/genhtml/index.html](https://581.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-06 02:55:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/581#pullrequestreview-1132248478  

📁 include/boost/url/detail/impl/any_segments_iter.ipp

```diff
 130 |+     auto cs = pchars;
 131 |+     if (encode_colons)
 132 |+         cs = cs - ':';
```

> Username: vinniefalco  
> Created_at: 2022-10-06 02:55:26 UTC  
> Updated_at: 2022-10-06 02:55:27 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r988506502  

There needs to be two character set constants, constexpr, otherwise we are running code needlessly


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-06 03:01:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/581#pullrequestreview-1132251902  

📁 include/boost/url/impl/url_base.ipp

```diff
 203 |+         po - sn);
 204 |+     // move [id_path, id_query) left
 205 |+     std::size_t qo = impl_.offset(id_query);
```

> Username: vinniefalco  
> Created_at: 2022-10-06 03:01:04 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r988508857  

why not `auto`


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-06 03:01:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/581#pullrequestreview-1132252323  

📁 include/boost/url/impl/url_base.ipp

```diff
 234 |+                it != end)
 235 |+             ++it;
 236 |+         std::memmove(it + (2 * cn), it, end - it);
```

> Username: vinniefalco  
> Created_at: 2022-10-06 03:01:56 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r988509191  

You can't just use `memmove` you have to go through the `op` or else overlapping string parameters won't work

> Username: alandefreitas  
> Created_at: 2022-10-06 17:52:42 UTC  
> Updated_at: 2022-10-06 17:52:43 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r989332548  

I think it doesn't make any difference in this case because `it` and `end` are both internal.

> Username: vinniefalco  
> Created_at: 2022-10-06 22:12:36 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r989522063  

if it doesn't make any difference then leave a comment explaining it


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-06 03:02:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/581#pullrequestreview-1132252594  

📁 include/boost/url/impl/url_base.ipp

```diff
 251 |+             else
 252 |+             {
 253 |+                 *dest-- = 'A';
```

> Username: vinniefalco  
> Created_at: 2022-10-06 03:02:29 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r988509385  

The user doesn't have a way to choose upper or lower case

> Username: alandefreitas  
> Created_at: 2022-10-06 17:54:00 UTC  
> Updated_at: 2022-10-06 17:54:09 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r989333527  

How would that happen? An extra parameter just in case this is required?  
  
My initial reasoning they're supposed to choose since normalization requires upper case and this is a form of normalization.

> Username: vinniefalco  
> Created_at: 2022-10-06 22:12:25 UTC  
> Updated_at: 2022-10-06 22:12:26 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r989521983  

we might not have a good way to let them pick it but I am just pointing it out. you should leave a comment


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-06 03:04:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/581#pullrequestreview-1132253751  

📁 include/boost/url/impl/url_base.ipp

```diff
 351 |+                    it != end)
 352 |+                 ++it;
 353 |+             std::memmove(it + (2 * cn), it, end - it);
```

> Username: vinniefalco  
> Created_at: 2022-10-06 03:04:55 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r988510244  

this needs to be `op.move` (or w/e it is called)

> Username: alandefreitas  
> Created_at: 2022-10-06 17:54:24 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r989333879  

https://github.com/boostorg/url/pull/581#discussion_r989332548


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-06 03:09:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/581#pullrequestreview-1132255830  

📁 include/boost/url/impl/url_base.ipp

```diff
 187 |+     auto pn = impl_.len(id_path);
 188 |+     std::size_t cn = 0;
 189 |+     for (char c: first_segment())
```

> Username: vinniefalco  
> Created_at: 2022-10-06 03:09:21 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r988511738  

how many times are you calling `first_segment` in one function?


---

## Comment 12

> Username: alandefreitas  
> Created_at: 2022-10-06 17:54:45 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1270472218  

There was a case I missed in `normalize_path`. It should be good now.

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-10-06 17:58:39 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1270476377  

GCOVR code coverage report [https://581.url.prtest.cppalliance.org/gcovr/index.html](https://581.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://581.url.prtest.cppalliance.org/genhtml/index.html](https://581.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2022-10-06 20:33:36 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1270645519  

GCOVR code coverage report [https://581.url.prtest.cppalliance.org/gcovr/index.html](https://581.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://581.url.prtest.cppalliance.org/genhtml/index.html](https://581.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2022-10-06 21:38:19 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1270725485  

GCOVR code coverage report [https://581.url.prtest.cppalliance.org/gcovr/index.html](https://581.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://581.url.prtest.cppalliance.org/genhtml/index.html](https://581.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2022-10-06 22:28:20 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1270765502  

GCOVR code coverage report [https://581.url.prtest.cppalliance.org/gcovr/index.html](https://581.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://581.url.prtest.cppalliance.org/genhtml/index.html](https://581.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2022-10-12 19:03:34 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1276611527  

GCOVR code coverage report [https://581.url.prtest.cppalliance.org/gcovr/index.html](https://581.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://581.url.prtest.cppalliance.org/genhtml/index.html](https://581.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2022-10-12 23:41:46 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1276849487  

GCOVR code coverage report [https://581.url.prtest.cppalliance.org/gcovr/index.html](https://581.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://581.url.prtest.cppalliance.org/genhtml/index.html](https://581.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2022-10-13 16:29:02 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1277883047  

GCOVR code coverage report [https://581.url.prtest.cppalliance.org/gcovr/index.html](https://581.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://581.url.prtest.cppalliance.org/genhtml/index.html](https://581.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2022-10-18 18:09:02 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1282809716  

GCOVR code coverage report [https://581.url.prtest.cppalliance.org/gcovr/index.html](https://581.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://581.url.prtest.cppalliance.org/genhtml/index.html](https://581.url.prtest.cppalliance.org/genhtml/index.html)

---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-18 19:07:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/581#pullrequestreview-1146373610  

📁 include/boost/url/detail/any_segments_iter.hpp

```diff
  47 |+     int fast_nseg = 0;
  48 |+ 
  49 |+     bool encode_colons = false;
```

> Username: vinniefalco  
> Created_at: 2022-10-18 19:07:04 UTC  
> Updated_at: 2022-10-18 19:07:05 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r998610521  

well the way you pass it in the ctor, it looks like it doesn't need to be public. I was fine with just letting `edit_segments` set the data member directly, because it is less code (no need to repeat all the boilerplate in the derived iterator constructors). What do you want to do?

> Username: alandefreitas  
> Created_at: 2022-10-18 21:09:25 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r998715089  

Other functions depend on accessing both fast_nseg and encode_colons directly, like all other members. `edit_segments` depends on a lot of information to determine if encode_colons should be true.

> Username: vinniefalco  
> Created_at: 2022-10-18 22:46:14 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r998777163  

I was wrong... I misread. It wasn't the ctor it was the copy_impl and measure_impl.

> Username: vinniefalco  
> Created_at: 2022-10-19 18:53:38 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r999829042  

rename to `escape_colons` and in url_base.ipp please :)

> Username: vinniefalco  
> Created_at: 2022-10-19 19:10:53 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r999843789  

or not... but at least put a comment on the data member.

> Username: alandefreitas  
> Created_at: 2022-10-19 20:59:35 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r999928947  

done

> Username: vinniefalco  
> Created_at: 2022-10-19 23:21:14 UTC  
> Url: https://github.com/boostorg/url/pull/581#discussion_r1000011762  

LOL...


---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2022-10-18 22:40:53 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1283092496  

GCOVR code coverage report [https://581.url.prtest.cppalliance.org/gcovr/index.html](https://581.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://581.url.prtest.cppalliance.org/genhtml/index.html](https://581.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2022-10-19 21:08:53 UTC  
> Url: https://github.com/boostorg/url/pull/581#issuecomment-1284576515  

GCOVR code coverage report [https://581.url.prtest.cppalliance.org/gcovr/index.html](https://581.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://581.url.prtest.cppalliance.org/genhtml/index.html](https://581.url.prtest.cppalliance.org/genhtml/index.html)

---
