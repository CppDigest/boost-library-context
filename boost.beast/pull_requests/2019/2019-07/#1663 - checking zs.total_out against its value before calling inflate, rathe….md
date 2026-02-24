# #1663 checking zs.total_out against its value before calling inflate, rathe… [Closed]

> Username: dmorilha-twilio  
> Created at: 2019-07-22 21:35:57 UTC  
> Updated at: 2020-03-10 02:07:52 UTC  
> Closed at: 2020-03-10 02:07:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1663  

…r than 0

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2019-07-22 22:14:16 UTC  
> Url: https://github.com/boostorg/beast/pull/1663#issuecomment-513975306  

Any chance you could put together a test?

---

## Comment 2

> Username: dmorilha-twilio  
> Created_at: 2019-07-22 23:11:09 UTC  
> Url: https://github.com/boostorg/beast/pull/1663#issuecomment-513988949  

I would love to, but I don't know where to start... any pointers?

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2019-07-22 23:16:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1663#issuecomment-513989946  

Yes, have a look at how the existing tests are written:  
https://github.com/boostorg/beast/blob/develop/test/beast/websocket/read1.cpp#L63

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2019-07-23 00:37:58 UTC  
> Url: https://github.com/boostorg/beast/pull/1663#issuecomment-514007343  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1663?src=pr&el=h1) Report  
> Merging [#1663](https://codecov.io/gh/boostorg/beast/pull/1663?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/2ed1c92e036c70aca611315d476f60443d4cc28d?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `50%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1663/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1663?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1663      +/-   ##  
===========================================  
+ Coverage    92.57%   92.57%   +<.01%       
===========================================  
  Files          156      156                
  Lines        11904    11906       +2       
===========================================  
+ Hits         11020    11022       +2       
  Misses         884      884  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1663?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/1663/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `86.53% <50%> (+0.04%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1663?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1663?src=pr&el=footer). Last update [2ed1c92...73cea20](https://codecov.io/gh/boostorg/beast/pull/1663?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 5

> Username: stale[bot]  
> Created_at: 2019-08-22 00:46:21 UTC  
> Url: https://github.com/boostorg/beast/pull/1663#issuecomment-523702008  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2019-09-09 23:32:53 UTC  
> Url: https://github.com/boostorg/beast/pull/1663#issuecomment-529706497  

@djarek is a test possible for this?

---

## Comment 7

> Username: djarek  
> Created_at: 2019-09-16 09:08:06 UTC  
> Updated_at: 2019-09-16 09:08:38 UTC  
> Url: https://github.com/boostorg/beast/pull/1663#issuecomment-531696951  

Is there a way to force Beast's websocket stream to send an incomplete fragmented message (without sending the final frame) followed by a close frame?  
  
If not, then the only way to test this would be to manually send one using the lower layer.

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2019-09-16 09:17:11 UTC  
> Url: https://github.com/boostorg/beast/pull/1663#issuecomment-531699713  

> Is there a way to force Beast's websocket stream to send an incomplete fragmented message  
  
Yeah, I think you just call `write_some` without indicating that it is the final fragment. Then call close.

---

## Comment 9

> Username: stale[bot]  
> Created_at: 2019-10-16 10:09:07 UTC  
> Url: https://github.com/boostorg/beast/pull/1663#issuecomment-542629590  

This issue has been open for a while with no activity, has it been resolved?

---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2019-10-16 11:35:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1663#pullrequestreview-302505907  

📁 include/boost/beast/websocket/impl/read.hpp

```diff
 499 |                             // https://github.com/madler/zlib/issues/280
 499 |-                             if(zs.total_out > 0)
 500 |+                             if(zs.total_out > total_out)
```

> Username: vinniefalco  
> Created_at: 2019-10-16 11:35:27 UTC  
> Updated_at: 2019-10-16 11:35:28 UTC  
> Url: https://github.com/boostorg/beast/pull/1663#discussion_r335421600  

I'm not sure this does anything, isn't `zs.total_out` always 0 at line 495? `zs` is always newly constructed on line 465, which sets `total_out` to `0`. Or am I missing something?

> Username: AeroStun  
> Created_at: 2020-03-10 00:27:18 UTC  
> Url: https://github.com/boostorg/beast/pull/1663#discussion_r390032347  

You're not missing anything, it is always initially 0; confirmed not a bug.


---

## Comment 11

> Username: vinniefalco  
> Created_at: 2019-11-02 15:30:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1663#issuecomment-549054107  

This doesn't look like a bug to me, can you please address my comment?

---

## Comment 12

> Username: vinniefalco  
> Created_at: 2020-03-10 02:07:52 UTC  
> Url: https://github.com/boostorg/beast/pull/1663#issuecomment-596864237  

I'm closing this, since it seems like it is not a bug - feel free to reopen if we have new information (or if I was mistaken in closing it). Thanks!

---
