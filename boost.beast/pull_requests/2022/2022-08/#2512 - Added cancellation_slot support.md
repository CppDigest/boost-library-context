# #2512 Added cancellation_slot support. [Merged]

> Username: klemens-morgenstern  
> Created at: 2022-08-31 02:06:21 UTC  
> Updated at: 2022-10-27 14:56:24 UTC  
> Merged at: 2022-10-27 14:56:19 UTC  
> Closed at: 2022-10-27 14:56:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2512  

Validating & implementing support for asio::cancellation_slot.  
  
Implements #2325

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2022-08-31 04:50:04 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1232459318  

The basic_stream functions are all inheriting async_base and are used in the main op. This will work fine with cancellation.   
  
It was mainly the wrappers (bind_front) and the write_op composed op that needed some help.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2022-08-31 07:23:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1232560924  

This will need more validation work and there are a few gaps, mainly when the websockets does a ` post`. that's technically a race where we might miss a cancellation. I'll need some help on it's composition to figure that out though.

---

## Review 3 [Commented]

> Username: madmongo1  
> Created_at: 2022-08-31 08:02:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2512#pullrequestreview-1091452792  

📁 include/boost/beast/http/impl/write.hpp

```diff
 239 |                 bytes_transferred_ += bytes_transferred;
 240 |+                 if (!ec && st_.cancelled() != net::cancellation_type::none)
 241 |+                     ec = net::error::operation_aborted;
```

> Username: madmongo1  
> Created_at: 2022-08-31 08:00:49 UTC  
> Updated_at: 2022-08-31 08:02:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#discussion_r959278027  

I suspect this should not cancel when cancellation type is total.  
does the op need to apply a filter to the cancellation state to prevent total cancellation (since it can't be supported)?  
partial would be ok  
terminal also ok

> Username: klemens-morgenstern  
> Created_at: 2022-08-31 08:08:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#discussion_r959285569  

It's `terminal` by filter in the state already.  
  
https://github.com/chriskohlhoff/asio/blob/fc901d79c11c72a0ba81764820d3c6a788c0f075/asio/include/asio/cancellation_state.hpp#L88


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-10-02 14:47:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1264660829  

![pullrequest](https://2512.beast.tracing.cppalliance.org/pullrequest-3da33723.png)  
Timeline tracing charts: [https://2512.beast.tracing.cppalliance.org/index.html](https://2512.beast.tracing.cppalliance.org/index.html)

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2022-10-02 15:56:12 UTC  
> Updated_at: 2022-10-27 06:32:23 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1264675060  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2512?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2512](https://codecov.io/gh/boostorg/beast/pull/2512?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f2e85a8) into [develop](https://codecov.io/gh/boostorg/beast/commit/5db6da9aa452a19c0420336ff25b7eafdf87df4e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5db6da9) will **increase** coverage by `0.00%`.  
> The diff coverage is `83.43%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2512/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2512?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2512   +/-   ##  
========================================  
  Coverage    94.69%   94.70%             
========================================  
  Files          153      154    +1       
  Lines        11928    12019   +91       
========================================  
+ Hits         11295    11382   +87       
- Misses         633      637    +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2512?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/basic\_stream.hpp](https://codecov.io/gh/boostorg/beast/pull/2512/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2Jhc2ljX3N0cmVhbS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/detect\_ssl.hpp](https://codecov.io/gh/boostorg/beast/pull/2512/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGVjdF9zc2wuaHBw) | `93.75% <0.00%> (ø)` | |  
| [include/boost/beast/core/saved\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/2512/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL3NhdmVkX2hhbmRsZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/http/buffer\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/2512/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2J1ZmZlcl9ib2R5LmhwcA==) | `86.36% <0.00%> (ø)` | |  
| [include/boost/beast/http/empty\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/2512/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2VtcHR5X2JvZHkuaHBw) | `71.42% <0.00%> (ø)` | |  
| [include/boost/beast/http/parser.hpp](https://codecov.io/gh/boostorg/beast/pull/2512/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `81.96% <0.00%> (ø)` | |  
| [include/boost/beast/http/string\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/2512/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `89.47% <0.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/ssl.hpp](https://codecov.io/gh/boostorg/beast/pull/2512/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zc2wuaHBw) | `0.00% <ø> (ø)` | |  
| [include/boost/beast/websocket/stream.hpp](https://codecov.io/gh/boostorg/beast/pull/2512/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/websocket/impl/handshake.hpp](https://codecov.io/gh/boostorg/beast/pull/2512/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9oYW5kc2hha2UuaHBw) | `96.72% <33.33%> (ø)` | |  
| ... and [26 more](https://codecov.io/gh/boostorg/beast/pull/2512/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2512?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2512?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0bf3d97...f2e85a8](https://codecov.io/gh/boostorg/beast/pull/2512?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-10-02 19:28:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1264715542  

1. does each handler need a cancellation slot? why not put it on the stream (or is that nonsensical)?  
  
2. will this be squashed

---

## Comment 7

> Username: klemens-morgenstern  
> Created_at: 2022-10-03 01:19:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1264805469  

1. This is per-op cancellation, not stream cancellation  
2. Yes.

---

## Comment 8

> Username: klemens-morgenstern  
> Created_at: 2022-10-03 01:21:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1264806402  

@madmongo1 can you review the current version?

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2022-10-03 01:22:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1264807544  

individual ops cannot be reliably canceled though, and there's no way to know what state the stream is in afterwards

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-10-04 02:47:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1266324446  

![pullrequest](https://2512.beast.tracing.cppalliance.org/pullrequest-97f0ba50.png)  
Timeline tracing charts: [https://2512.beast.tracing.cppalliance.org/index.html](https://2512.beast.tracing.cppalliance.org/index.html)

---

## Comment 11

> Username: klemens-morgenstern  
> Created_at: 2022-10-04 04:17:23 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1266376319  

Cancelling http::async_read is currently unreliable, because the cancellation state of a pr is protected - probably just a mistake in asio, will make a pr.

---

## Comment 12

> Username: vinniefalco  
> Created_at: 2022-10-10 14:48:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1273431637  

Are you going to squash this?

---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-10 14:49:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2512#pullrequestreview-1136147390  

📁 include/boost/beast/_experimental/test/impl/stream.hpp

```diff
  78 |         }
  79 | 
  80 |+         using cancellation_slot_type =
```

> Username: vinniefalco  
> Created_at: 2022-10-10 14:49:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#discussion_r991372918  

do we need to support cancelation for the test stream?

> Username: klemens-morgenstern  
> Created_at: 2022-10-10 16:14:17 UTC  
> Updated_at: 2022-10-10 16:14:18 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#discussion_r991455271  

I am using tcp streams on purpose so we don't.

> Username: vinniefalco  
> Created_at: 2022-10-10 16:15:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#discussion_r991456521  

but it looks like we are adding cancelation to the test stream here?


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-10-10 14:49:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2512#pullrequestreview-1136148205  

📁 include/boost/beast/_experimental/test/impl/stream.hpp

```diff
  82 |+ 
  83 |+         cancellation_slot_type
  84 |+         get_cancellation_slot() const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-10-10 14:49:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#discussion_r991373446  

What if people are currently using `websocket::stream` with a NextLayer that doesn't support cancelation slots? Will they get a compile error about a missing nested type and `get_cancellation_slot` function?

> Username: klemens-morgenstern  
> Created_at: 2022-10-10 16:14:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#discussion_r991455722  

No, it will just get ignored. It's a property on the handler, not the stream


---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2022-10-10 16:58:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1273593262  

![pullrequest](https://2512.beast.tracing.cppalliance.org/pullrequest-a5cbb4ed.png)  
Timeline tracing charts: [https://2512.beast.tracing.cppalliance.org/index.html](https://2512.beast.tracing.cppalliance.org/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2022-10-11 09:06:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1274361398  

![pullrequest](https://2512.beast.tracing.cppalliance.org/pullrequest-7b6e95f9.png)  
Timeline tracing charts: [https://2512.beast.tracing.cppalliance.org/index.html](https://2512.beast.tracing.cppalliance.org/index.html)

---

## Comment 17

> Username: klemens-morgenstern  
> Created_at: 2022-10-11 14:03:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1274742638  

@sehe @madmongo1 I think it's ready, so I could use another review, since this is stuff can be critical.

---

## Comment 18

> Username: klemens-morgenstern  
> Created_at: 2022-10-14 05:24:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1278500273  

Note to self: document which op supports what kind of cancellation. Most likely only `terminal` everywhere. Reference:  
  
https://github.com/chriskohlhoff/asio/blob/master/asio/include/asio/ssl/stream.hpp#L567

---

## Comment 19

> Username: madmongo1  
> Created_at: 2022-10-15 13:02:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1279741392  

Is it possible to squash all the commits that relate to cancelation?

---

## Comment 20

> Username: klemens-morgenstern  
> Created_at: 2022-10-16 07:48:28 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1279914381  

Note to self: Verify partial cancellation doesn't break the `websocket::async_read_some` and `websocket::async_writes_some`

---

## Review 21 [Commented]

> Username: klemens-morgenstern  
> Created_at: 2022-10-16 08:07:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2512#pullrequestreview-1143264416  

📁 include/boost/beast/websocket/impl/close.hpp

```diff
 153 |+                 {
 154 |+                     // if a cancellation fires here, we do a dirty shutdown
 155 |+                     close_socket(get_lowest_layer(impl.stream()));
```

> Username: klemens-morgenstern  
> Created_at: 2022-10-16 07:56:57 UTC  
> Updated_at: 2022-10-16 08:07:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#discussion_r996405359  

add `        change_status(status::closed);  
`


---

## Comment 22

> Username: klemens-morgenstern  
> Created_at: 2022-10-16 08:25:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1279920415  

Sockets `.async_read_some` and `async_write_some` can be cancelled `partial`ly.  
  
https://www.boost.org/doc/libs/1_80_0/doc/html/boost_asio/reference/basic_stream_socket/async_read_some.html

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2022-10-17 13:48:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1280891850  

![pullrequest](https://2512.beast.tracing.cppalliance.org/pullrequest-010d8020.png)  
Timeline tracing charts: [https://2512.beast.tracing.cppalliance.org/index.html](https://2512.beast.tracing.cppalliance.org/index.html)

---

## Review 24 [Commented]

> Username: madmongo1  
> Created_at: 2022-10-17 14:53:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2512#pullrequestreview-1144308863  

📁 include/boost/beast/core/detail/filtering_cancellation_slot.hpp

```diff
  31 |+     {
  32 |+         Handler handler;
  33 |+         const net::cancellation_type type{net::cancellation_type::terminal};
```

> Username: madmongo1  
> Created_at: 2022-10-17 14:50:05 UTC  
> Updated_at: 2022-10-17 14:53:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#discussion_r997161585  

I personally hate this kind of initialisation.  
  
I find ` const net::cancellation_type type = net::cancellation_type::terminal` more readable.


📁 test/beast/core/filtering_cancellation_slot.cpp

```diff
  39 |+         BEAST_EXPECT(fired == ct::total);
  40 |+         sl.emit(ct::terminal);
  41 |+         BEAST_EXPECT(fired == ct::terminal);
```

> Username: madmongo1  
> Created_at: 2022-10-17 14:53:06 UTC  
> Updated_at: 2022-10-17 14:53:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#discussion_r997165116  

What is the expected behaviour if:  
1. slot is emitted with a type that is filtered out  
2. slot it subsequently emitted with a type that is not filtered  
  
Will the second signal reach the slot as expected?


---

## Comment 25

> Username: klemens-morgenstern  
> Created_at: 2022-10-17 17:27:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1281206621  

wrong cancellation type issue & docs are done. Should be ready to merge.

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2022-10-17 17:36:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1281223167  

An automated preview of the documentation is available at [https://2512.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2512.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 27

> Username: cppalliance-bot  
> Created_at: 2022-10-17 18:07:46 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1281271079  

An automated preview of the documentation is available at [https://2512.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2512.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 28

> Username: cppalliance-bot  
> Created_at: 2022-10-18 03:41:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1281772936  

An automated preview of the documentation is available at [https://2512.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2512.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 29

> Username: cppalliance-bot  
> Created_at: 2022-10-18 04:01:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1281783944  

An automated preview of the documentation is available at [https://2512.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2512.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 30

> Username: cppalliance-bot  
> Created_at: 2022-10-18 04:18:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1281794095  

![pullrequest](https://2512.beast.tracing.cppalliance.org/pullrequest-3fb3a11c.png)  
Timeline tracing charts: [https://2512.beast.tracing.cppalliance.org/index.html](https://2512.beast.tracing.cppalliance.org/index.html)

---

## Comment 31

> Username: cppalliance-bot  
> Created_at: 2022-10-27 05:26:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2512#issuecomment-1293014075  

An automated preview of the documentation is available at [https://2512.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2512.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---
