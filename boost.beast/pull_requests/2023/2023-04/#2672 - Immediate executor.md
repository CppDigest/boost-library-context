# #2672 Immediate executor [Merged]

> Username: klemens-morgenstern  
> Created at: 2023-04-24 11:30:38 UTC  
> Updated at: 2023-10-13 08:33:23 UTC  
> Merged at: 2023-10-13 08:33:23 UTC  
> Closed at: 2023-10-13 08:33:23 UTC  
> Url: https://github.com/boostorg/beast/pull/2672  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-04-24 12:01:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#issuecomment-1520019473  

![pullrequest](https://2672.beast.tracing.cppalliance.org/pullrequest-17d37d10.png)  
Timeline tracing charts: [https://2672.beast.tracing.cppalliance.org/index.html](https://2672.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2023-08-15 12:55:06 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#issuecomment-1678886788  

Note: `get_associated_immediate_executor` works with composed ops.

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-08-16 02:08:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#issuecomment-1679861049  

![pullrequest](https://2672.beast.tracing.cppalliance.org/pullrequest-d50d9e20.png)  
Timeline tracing charts: [https://2672.beast.tracing.cppalliance.org/index.html](https://2672.beast.tracing.cppalliance.org/index.html)

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2023-08-18 07:24:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#issuecomment-1683480146  

Specific tests are still needed. boost.async supports it, so that might be a useful first test.

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2023-08-18 08:27:34 UTC  
> Updated_at: 2023-10-12 14:19:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#issuecomment-1683554578  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2672](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (de158cf) into [develop](https://app.codecov.io/gh/boostorg/beast/commit/177a1222a60d1cdf5aff0690f3755786ae23f16d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (177a122) will **decrease** coverage by `0.05%`.  
> The diff coverage is `88.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2672/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2672      +/-   ##  
===========================================  
- Coverage    92.97%   92.92%   -0.05%       
===========================================  
  Files          177      178       +1       
  Lines        13658    13697      +39       
===========================================  
+ Hits         12698    12728      +30       
- Misses         960      969       +9       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/beast/\_experimental/http/icy\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL2h0dHAvaWN5X3N0cmVhbS5ocHA=) | `100.00% <ø> (ø)` | |  
| [...st/beast/\_experimental/test/immediate\_executor.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3QvaW1tZWRpYXRlX2V4ZWN1dG9yLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/\_experimental/test/stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9fZXhwZXJpbWVudGFsL3Rlc3Qvc3RyZWFtLmhwcA==) | `75.00% <ø> (ø)` | |  
| [include/boost/beast/core/async\_base.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2FzeW5jX2Jhc2UuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/beast/core/basic\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2Jhc2ljX3N0cmVhbS5ocHA=) | `80.39% <ø> (ø)` | |  
| [include/boost/beast/core/buffered\_read\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2J1ZmZlcmVkX3JlYWRfc3RyZWFtLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/detect\_ssl.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGVjdF9zc2wuaHBw) | `92.42% <ø> (ø)` | |  
| [include/boost/beast/core/flat\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfc3RyZWFtLmhwcA==) | `100.00% <ø> (ø)` | |  
| [...ude/boost/beast/core/impl/buffered\_read\_stream.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyZWRfcmVhZF9zdHJlYW0uaHBw) | `91.37% <100.00%> (+0.15%)` | :arrow_up: |  
| [include/boost/beast/http/impl/read.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvcmVhZC5ocHA=) | `98.46% <100.00%> (+0.04%)` | :arrow_up: |  
| ... and [8 more](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [177a122...de158cf](https://app.codecov.io/gh/boostorg/beast/pull/2672?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2023-10-04 05:58:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#issuecomment-1746187220  

@ashtum Can you review this?

---

## Review 7 [Commented]

> Username: ashtum  
> Created_at: 2023-10-04 09:01:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2672#pullrequestreview-1656816205  

📁 include/boost/beast/core/async_base.hpp

```diff
 315 |-         handler, then the associated cancellation_slot of the
 316 |-         derived class will be this type.
 333 |+     /** Returns the executor associated with this object.
```

> Username: ashtum  
> Created_at: 2023-10-04 07:15:52 UTC  
> Updated_at: 2023-10-04 09:01:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#discussion_r1345308115  

`Returns the immediate executor associated with this object`?

---

📁 include/boost/beast/core/async_base.hpp

```diff
 335 |+         If a class derived from @ref boost::beast::async_base is a completion
 336 |+         handler, then the object returned from this function will be used
 337 |+         as the associated executor of the derived class.
```

> Username: ashtum  
> Created_at: 2023-10-04 07:16:23 UTC  
> Updated_at: 2023-10-04 09:01:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#discussion_r1345308761  

`as the associated immediate executor of the derived class.`?

> Username: klemens-morgenstern  
> Created_at: 2023-10-05 05:08:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#discussion_r1346790024  

Update the comment


---

## Review 8 [Commented]

> Username: fpelliccioni  
> Created_at: 2023-10-09 07:36:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2672#pullrequestreview-1663867769  

📁 include/boost/beast/core/async_base.hpp

```diff
 343 |+     {
 344 |+       return net::get_associated_immediate_executor(
 345 |+           h_, wg1_.get_executor());
```

> Username: fpelliccioni  
> Created_at: 2023-10-09 07:36:52 UTC  
> Updated_at: 2023-10-09 07:36:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#discussion_r1349948745  

NIT: formatting   
```diff  
-          h_, wg1_.get_executor());  
+          return net::get_associated_immediate_executor(  
+               h_, wg1_.get_executor());  
```


---

## Review 9 [Commented]

> Username: fpelliccioni  
> Created_at: 2023-10-09 07:38:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2672#pullrequestreview-1663870680  

📁 include/boost/beast/websocket/impl/read.hpp

```diff
 241 |-                                     net::post(sp->stream().get_executor(), std::move(*this));
 241 |+                                     const auto ex = this->get_immediate_executor();
 242 |+                     net::dispatch(ex, std::move(*this));
```

> Username: fpelliccioni  
> Created_at: 2023-10-09 07:38:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#discussion_r1349950661  

```diff  
-                    net::dispatch(ex, std::move(*this));  
+                                    const auto ex = this->get_immediate_executor();  
+                                    net::dispatch(ex, std::move(*this));  
```


---

## Review 10 [Commented]

> Username: fpelliccioni  
> Created_at: 2023-10-09 07:39:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2672#pullrequestreview-1663871081  

📁 include/boost/beast/websocket/impl/read.hpp

```diff
 294 |-                                 net::post(sp->stream().get_executor(), std::move(*this));
 295 |+                                 const auto ex = this->get_immediate_executor();
 296 |+                     net::dispatch(ex, std::move(*this));
```

> Username: fpelliccioni  
> Created_at: 2023-10-09 07:39:09 UTC  
> Updated_at: 2023-10-09 07:39:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#discussion_r1349950966  

```diff  
-                    net::dispatch(ex, std::move(*this));  
+                                const auto ex = this->get_immediate_executor();  
+                                net::dispatch(ex, std::move(*this));  
```


---

## Review 11 [Commented]

> Username: fpelliccioni  
> Created_at: 2023-10-09 07:39:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2672#pullrequestreview-1663871444  

📁 include/boost/beast/websocket/impl/read.hpp

```diff
 338 |-                                     net::post(sp->stream().get_executor(), std::move(*this));
 340 |+                                     const auto ex = this->get_immediate_executor();
 341 |+                     net::dispatch(ex, std::move(*this));
```

> Username: fpelliccioni  
> Created_at: 2023-10-09 07:39:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#discussion_r1349951181  

```diff  
-                    net::dispatch(ex, std::move(*this));  
+                                    const auto ex = this->get_immediate_executor();  
+                                    net::dispatch(ex, std::move(*this));  
```


---

## Review 12 [Commented]

> Username: fpelliccioni  
> Created_at: 2023-10-09 07:39:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2672#pullrequestreview-1663871755  

📁 include/boost/beast/websocket/impl/read.hpp

```diff
 369 |-                                     net::post(sp->stream().get_executor(), std::move(*this));
 372 |+                                     const auto ex = this->get_immediate_executor();
 373 |+                     net::dispatch(ex, std::move(*this));
```

> Username: fpelliccioni  
> Created_at: 2023-10-09 07:39:37 UTC  
> Updated_at: 2023-10-09 07:39:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#discussion_r1349951359  

```diff  
-                    net::dispatch(ex, std::move(*this));  
+                                    const auto ex = this->get_immediate_executor();  
+                                    net::dispatch(ex, std::move(*this));  
```


---

## Comment 13

> Username: fpelliccioni  
> Created_at: 2023-10-09 08:44:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#issuecomment-1752574827  

The changes in the code appear to be consistent and well-structured.  
  
Updates made to the function comments, which generate documentation, are appropriate and coherent.  
  
There don't seem to be significant changes to the API, with the exception of async_base::get_immediate_executor(). Given this minor adjustment, the tests appear to remain effective and relevant.  
  
Codecov has identified several newly added lines of code that aren’t covered by tests. While I’m not entirely certain, these seem to be false positives. It would be beneficial to review and confirm these findings, either by adding the necessary tests or silencing irrelevant warnings. For example:  
  
```  
Codecov  
/ codecov/patch  
include/boost/beast/http/impl/write.hpp#L224-L225  
Added lines #L224 - L225 were not covered by tests  
```  
  
The PR lacks a description. I recommend adding a brief description to assist both current reviewers and anyone looking at this PR in the future. This description should include the rationale behind the proposed changes (even if it seems obvious), and whether the changes resolve or address any specific issues or problems.  
  
In summary, this PR appears to be mostly well-structured and presented. The most crucial step at this point would be to address the areas identified by Codecov and to enhance the PR description to provide clear context and complete understanding to reviewers and future observers.  
  
Good job 🚀

---

## Review 14 [Commented]

> Username: ashtum  
> Created_at: 2023-10-12 07:27:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2672#pullrequestreview-1673459177  

📁 include/boost/beast/core/async_base.hpp

```diff
  25 | #include <boost/asio/handler_invoke_hook.hpp>
  26 |+ #include <boost/asio/dispatch.hpp>
  27 | #include <boost/asio/post.hpp>
```

> Username: ashtum  
> Created_at: 2023-10-12 07:27:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2672#discussion_r1356386836  

Unused header.


---
