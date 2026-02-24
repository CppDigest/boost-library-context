# #2480 per-message compression options [Closed]

> Username: alandefreitas  
> Created at: 2022-07-05 23:21:11 UTC  
> Updated at: 2022-10-03 01:38:27 UTC  
> Closed at: 2022-10-03 01:38:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2480  

fix #226, #227

---

## Review 1 [Commented]

> Username: alandefreitas  
> Created_at: 2022-07-05 23:23:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2480#pullrequestreview-1029313648  

📁 include/boost/beast/websocket/option.hpp

```diff
  58 |+ 
  59 |+     /// The minimum size a message should have to be compressed
  60 |+     std::size_t msg_size_threshold = 0;
```

> Username: alandefreitas  
> Created_at: 2022-07-05 23:23:00 UTC  
> Url: https://github.com/boostorg/beast/pull/2480#discussion_r914279283  

I couldn't find a reference for a reasonable default yet...


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-05 23:25:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2480#pullrequestreview-1029314865  

📁 include/boost/beast/websocket/impl/write.hpp

```diff
  88 |         {
  89 |-             impl.begin_msg();
  89 |+             impl.begin_msg(buffer_bytes(bs));
```

> Username: vinniefalco  
> Created_at: 2022-07-05 23:25:23 UTC  
> Updated_at: 2022-07-05 23:25:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2480#discussion_r914280197  

You need to inhibit ADL here, because `bs` is user-defined


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-05 23:25:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2480#pullrequestreview-1029314944  

📁 include/boost/beast/websocket/impl/write.hpp

```diff
 305 |             // send a single frame using multiple writes
 306 |-             remain_ = beast::buffer_bytes(cb_);
 306 |+             remain_ = buffer_bytes(cb_);
```

> Username: vinniefalco  
> Created_at: 2022-07-05 23:25:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2480#discussion_r914280276  

You need to inhibit ADL here


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-05 23:25:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2480#pullrequestreview-1029315041  

📁 include/boost/beast/websocket/impl/write.hpp

```diff
 624 |     {
 625 |-         impl.begin_msg();
 625 |+         impl.begin_msg(buffer_bytes(buffers));
```

> Username: vinniefalco  
> Created_at: 2022-07-05 23:25:46 UTC  
> Url: https://github.com/boostorg/beast/pull/2480#discussion_r914280336  

you might need to inhibit ADL here


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-05 23:27:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2480#pullrequestreview-1029315734  

📁 include/boost/beast/websocket/stream_fwd.hpp

```diff
  21 | namespace websocket {
  22 | 
  21 |- #ifndef BOOST_BEAST_DOXYGEN
```

> Username: vinniefalco  
> Created_at: 2022-07-05 23:27:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2480#discussion_r914280900  

Why did you remove the ifdef? This will probably confuse doxygen

> Username: alandefreitas  
> Created_at: 2022-07-05 23:57:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2480#discussion_r914291995  

I didn't remove it. I just moved it outside the quickbook comment to fix this small bug in the docs that were rendering `BOOST_BEAST_DOXYGEN` sometimes.  
  
![image](https://user-images.githubusercontent.com/5369819/177435458-2eea70c2-d172-449e-af8c-d9f1d6bd9b74.png)  
  
To be honest, I just checked https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/using_websocket.html and it seems like this only happens locally. I have no idea why.


---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-07-05 23:48:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2480#issuecomment-1175608473  

![pullrequest](https://2480.beast.tracing.cppalliance.org/pullrequest-6ba5cfc3.png)  
Timeline tracing charts: [https://2480.beast.tracing.cppalliance.org/index.html](https://2480.beast.tracing.cppalliance.org/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-07-06 00:21:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2480#issuecomment-1175624934  

![pullrequest](https://2480.beast.tracing.cppalliance.org/pullrequest-187c9b53.png)  
Timeline tracing charts: [https://2480.beast.tracing.cppalliance.org/index.html](https://2480.beast.tracing.cppalliance.org/index.html)

---

## Comment 8

> Username: klemens-morgenstern  
> Created_at: 2022-09-24 04:28:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2480#issuecomment-1256853153  

Looks to be failing due to #2481.

---

## Comment 9

> Username: codecov[bot]  
> Created_at: 2022-10-02 14:18:24 UTC  
> Updated_at: 2022-10-02 14:35:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2480#issuecomment-1264654833  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2480?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2480](https://codecov.io/gh/boostorg/beast/pull/2480?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (10ad742) into [develop](https://codecov.io/gh/boostorg/beast/commit/00293a6adb5383fe14217a8916dd7ff79a857483?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (00293a6) will **increase** coverage by `0.04%`.  
> The diff coverage is `82.60%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2480/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2480?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2480      +/-   ##  
===========================================  
+ Coverage    94.75%   94.80%   +0.04%       
===========================================  
  Files          152      152                
  Lines        11868    12290     +422       
===========================================  
+ Hits         11246    11651     +405       
- Misses         622      639      +17       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2480?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/2480/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/websocket/detail/impl\_base.hpp](https://codecov.io/gh/boostorg/beast/pull/2480/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvZGV0YWlsL2ltcGxfYmFzZS5ocHA=) | `85.71% <50.00%> (-0.78%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/2480/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW0uaHBw) | `73.78% <60.00%> (-0.71%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/stream\_impl.hpp](https://codecov.io/gh/boostorg/beast/pull/2480/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW1faW1wbC5ocHA=) | `96.41% <100.00%> (+0.01%)` | :arrow_up: |  
| [include/boost/beast/websocket/impl/write.hpp](https://codecov.io/gh/boostorg/beast/pull/2480/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC93cml0ZS5ocHA=) | `98.50% <100.00%> (ø)` | |  
| [include/boost/beast/http/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2480/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZXJyb3IuaXBw) | `94.11% <0.00%> (-1.01%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2480/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.72% <0.00%> (-0.44%)` | :arrow_down: |  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/boostorg/beast/pull/2480/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `94.85% <0.00%> (+0.25%)` | :arrow_up: |  
| [include/boost/beast/core/impl/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/2480/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvbXVsdGlfYnVmZmVyLmhwcA==) | `97.94% <0.00%> (+0.35%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2480?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2480?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [00293a6...10ad742](https://codecov.io/gh/boostorg/beast/pull/2480?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 10

> Username: klemens-morgenstern  
> Created_at: 2022-10-02 14:22:01 UTC  
> Url: https://github.com/boostorg/beast/pull/2480#issuecomment-1264655588  

Drone passes now, the 4.8 issues seems fixed. @vinniefalco where are we at with this?

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2022-10-02 19:31:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2480#issuecomment-1264716272  

Should be ok

---

## Comment 12

> Username: klemens-morgenstern  
> Created_at: 2022-10-03 01:38:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2480#issuecomment-1264819476  

Commited directly as squash

---
