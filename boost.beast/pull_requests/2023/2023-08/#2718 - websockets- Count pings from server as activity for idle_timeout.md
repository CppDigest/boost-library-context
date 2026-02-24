# #2718 websockets: Count pings from server as activity for idle_timeout [Closed]

> Username: xim  
> Created at: 2023-08-11 12:21:44 UTC  
> Updated at: 2025-02-08 12:18:36 UTC  
> Closed at: 2025-02-08 12:18:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2718  

If the stream is receiving control packets like ping, don't count it as idle. This means you can enable `timeout_opt.keep_alive_ping` on only one side to get heartbeat.  
  
Addresses issue #2716

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-08-11 13:24:14 UTC  
> Updated_at: 2024-02-19 13:55:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1674791271  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2718?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`e42cf8a`)](https://app.codecov.io/gh/boostorg/beast/commit/e42cf8a1e4b75ee33210a20837d06887809b3c67?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.03% compared to head [(`c8ffce2`)](https://app.codecov.io/gh/boostorg/beast/pull/2718?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 93.03%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2718/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2718?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2718      +/-   ##  
===========================================  
- Coverage    93.03%   93.03%   -0.01%       
===========================================  
  Files          178      178                
  Lines        13687    13684       -3       
===========================================  
- Hits         12734    12731       -3       
  Misses         953      953                
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/beast/pull/2718?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/stream\_impl.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2718?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9zdHJlYW1faW1wbC5ocHA=) | `96.16% <100.00%> (-0.03%)` | :arrow_down: |  
| [include/boost/beast/websocket/stream\_base.hpp](https://app.codecov.io/gh/boostorg/beast/pull/2718?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvc3RyZWFtX2Jhc2UuaHBw) | `26.31% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2718?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2718?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e42cf8a...c8ffce2](https://app.codecov.io/gh/boostorg/beast/pull/2718?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2023-08-11 13:51:59 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1674830210  

Can't this be configured already?  
  
```cpp  
beast::websocket::stream_base::timeout to;  
my_ws.get_option(to);  
to.keep_alive_pings = true;  
my_ws.set_option(to);  
```

---

## Comment 3

> Username: xim  
> Created_at: 2023-08-11 14:01:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1674845255  

This PR changes the behavior when `keep_alive_ping` is `false`.  
  
Without the change: receiving control frames does not count as "activity"  
  
With the change: receiving control frames counts as activity  
  
I could make that behaviour change another config member of the timeout struct if wanted.

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2023-08-11 14:06:01 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1674852272  

I understand that - but why? Isn't setting the option achieving the same thing? What's the difference?

---

## Comment 5

> Username: xim  
> Created_at: 2023-08-11 15:38:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1674987016  

Ah. The setting makes you send ping frames at an even interval. This change counts incoming control frames as activity regardless of whether or not you are sending pings yourself.

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2023-08-14 10:07:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1677046433  

Receiving control frames is not activity.   
  
So we would need another option to allow the pings to be counted as such. This all seems off to me, why don't you just send pings or increase the timeout?

---

## Comment 7

> Username: xim  
> Created_at: 2023-08-14 12:21:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1677218006  

I first reported this because I was confused by the `idle_timeout` / `keep_alive_pings` config options; Since a pong is activity when you've sent a ping, shouldn't a ping be activity as well?  
  
If not, timely disconnect detection needs ping and pong to be sent in both directions, doubling the number of packets sent/received for no additional benefit.  
  
Not a big issue, but I feel counting ping as activity is the more intuitive behaviour. Adding this as a `bool incoming_ping_keepalives = false` option also makes sense if keeping the old behaviour as default is a priority.

---

## Comment 8

> Username: xim  
> Created_at: 2023-08-30 12:07:28 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1699041989  

Updated the PR with tests, @ashtum  
  
I feel using a second wall clock time may be excessive, but seems to be somewhat in line with what other tests have done

---

## Comment 9

> Username: xim  
> Created_at: 2023-09-01 09:38:31 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1702466086  

Can/should I add/change documentation somehow? Is it sufficient that I update the inline documentation in `stream_base.hpp`?

---

## Comment 10

> Username: ashtum  
> Created_at: 2023-09-01 11:52:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1702626898  

Yes, please update the docs [here](https://github.com/boostorg/beast/tree/develop/doc/qbk/06_websocket) as well (if there is anything related).

---

## Comment 11

> Username: ashtum  
> Created_at: 2023-09-16 09:58:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1722193107  

@xim Anything I can help with?

---

## Comment 12

> Username: xim  
> Created_at: 2023-09-16 10:10:15 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1722195325  

Sorry, @ashtum, this just disappeared in the Big Pile of Stuff. I'll try to update docs on Monday

---

## Comment 13

> Username: xim  
> Created_at: 2023-10-02 07:06:04 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1742498469  

Apologies for forgetting about the documentation update. I rebased on develop, added documentation and pushed again.

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-10-02 07:12:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1742504472  

An automated preview of the documentation is available at [https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-10-02 09:32:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1742693137  

An automated preview of the documentation is available at [https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2023-10-02 10:27:09 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1742767212  

An automated preview of the documentation is available at [https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 17

> Username: xim  
> Created_at: 2023-10-02 12:10:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1742900444  

I'm happy with this now. If you want me to squash all commits, or fixup my fixup, or anything, just give the word =)

---

## Comment 18

> Username: klemens-morgenstern  
> Created_at: 2023-10-04 05:58:18 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1746186836  

This can be squashed on our side, this looks like a single commit.  
  
From what I can tell @vinniefalco approves the behavioural change, so everything looks good to me.

---

## Comment 19

> Username: xim  
> Created_at: 2023-10-04 13:10:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1746849992  

Looks like one of the tests has a timer that's 500ms and expects it to have been triggered after 600ms. I'll make it more permissive.

---

## Comment 20

> Username: xim  
> Created_at: 2023-10-04 13:14:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1746855301  

Made that 750ms, effectively bumping the slack from 100ms to 250ms

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2023-10-04 13:22:22 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1746869961  

An automated preview of the documentation is available at [https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 22

> Username: xim  
> Created_at: 2023-10-04 14:17:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1746972494  

Apologies for the noise. Tweaking these values is hard.

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2023-10-04 14:27:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1746991672  

An automated preview of the documentation is available at [https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2023-10-04 18:37:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1747437548  

An automated preview of the documentation is available at [https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 25

> Username: ashtum  
> Created_at: 2023-10-09 06:31:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1752414049  

@xim Why did you alter the timing values in the tests? Did you encounter any failures as a result of the tight timings?

---

## Comment 26

> Username: xim  
> Created_at: 2023-10-09 09:00:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1752598886  

Yes. I had to increase/change margins many times before all the different CI builders were green at the same time. The original timing worked once across the board, but not on the next run. The current timing should be stable.  
  
I'd love to see these tests run with stubbed time, for stability and execution speed. But don't know if that's readily available.

---

## Comment 27

> Username: ashtum  
> Created_at: 2023-11-19 17:01:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1817916662  

Hey @xim, I wanted to let you know that I've decided to review and merge this after the 1.84 release. I reckon it might be risky to do it for 1.84.

---

## Comment 28

> Username: xim  
> Created_at: 2023-11-19 20:06:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1817963150  

I have, for now, implemented most of the desired behaviour on top of the control callback. Looking forward to cleaning up that code ^_^

---

## Comment 29

> Username: vinniefalco  
> Created_at: 2023-11-24 12:07:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1825583683  

Why shouldn't control frames count as activity?

---

## Comment 30

> Username: ashtum  
> Created_at: 2023-11-24 17:31:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1825946442  

> Why shouldn't control frames count as activity?  
  
Did you mean, "Why should control frames count as activity?" If so, we had a discussion in https://github.com/boostorg/beast/issues/2716. The idea is that receiving any form of frame from the other side can be an indication that it is still active.

---

## Comment 31

> Username: vinniefalco  
> Created_at: 2023-11-24 20:12:58 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1826054687  

> Did you mean, "Why should control frames count as activity?"  
  
No. I am asking what the rationale would be, if any, to NOT count control frames as activity. I cannot think of one. But that doesn't mean it does not exist (even if the rationale is weak).

---

## Comment 32

> Username: xim  
> Created_at: 2023-11-24 21:38:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1826098175  

> I am asking what the rationale would be, if any, to NOT count control frames as activity.  
  
Maybe if you want to throw out sessions that "aren't doing anything" at an application level?  
  
Not arguing for this being sensible, just answering the hypothetical =)

---

## Comment 33

> Username: ashtum  
> Created_at: 2023-11-25 07:33:06 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1826240906  

Based on the source code, we have been treating the control frames as activity from the beginning (by setting idle_counter to zero). It is this branch in the code that has caused the timeout: https://github.com/boostorg/beast/blob/d0dd9c50694bcbb8836775e780eb046f66b3e1d7/include/boost/beast/websocket/impl/stream_impl.hpp#L572  
I don't know whether this behavior has been intentional or accidental.

---

## Comment 34

> Username: xim  
> Created_at: 2023-11-25 08:00:45 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1826248710  

> Based on the source code, we have been treating the control frames as activity from the beginning  
  
Yes, but only when the option `keep_alive_pings` is set. So pings and pongs only count as activity if you're *sending* pings.

---

## Comment 35

> Username: ashtum  
> Created_at: 2023-11-25 08:03:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1826249151  

> Yes, but only when the option `keep_alive_pings` is set. So pings and pongs only count as activity if you're _sending_ pings.  
  
I mean we have been setting `idle_counter` to zero unconditionally.

---

## Comment 36

> Username: xim  
> Created_at: 2023-11-26 10:21:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1826744206  

> I mean we have been setting `idle_counter` to zero unconditionally.  
  
Yes, but without `keep_alive_pings` the value of `idle_counter` is ignored and we go straight to timeout.

---

## Comment 37

> Username: ashtum  
> Created_at: 2023-11-26 10:33:31 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1826746670  

> Yes, but without `keep_alive_pings` the value of `idle_counter` is ignored and we go straight to timeout.  
  
Yeah, my point was that this might have been an unintentional behavior because we wanted to only send the ping message when `keep_alive_pings` is set and accidentally ignored the situation when `idle_counter` is zero and `keep_alive_pings` is unset.  
  
Besides that, other parts of the code show that we have been counting control frames as activity (by unconditionally setting idle_counter to zero).

---

## Comment 38

> Username: cppalliance-bot  
> Created_at: 2023-11-27 11:42:28 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1827670256  

An automated preview of the documentation is available at [https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 39

> Username: ashtum  
> Created_at: 2023-12-25 12:30:30 UTC  
> Updated_at: 2023-12-25 12:32:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1868964231  

@xim, could you please squash these commits into a single commit?  
And it seems there is still a problem in the unit tests for the ping operation: https://github.com/boostorg/beast/actions/runs/7004420416/job/19732998803?pr=2718#step:9:174

---

## Comment 40

> Username: xim  
> Created_at: 2023-12-26 12:56:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1869528967  

Yeah, the timing there is apparently night-impossible to get correct in a way that always passes on all platforms. In that one case, a ping hasn't been processed within 400ms. I can increase everything 25% again so it's 500ms. But is anyone has a way of running these tests with stubbed time, that would be preferable...

---

## Comment 41

> Username: cppalliance-bot  
> Created_at: 2023-12-26 13:13:32 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1869539063  

An automated preview of the documentation is available at [https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 42

> Username: xim  
> Created_at: 2023-12-26 16:58:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1869663883  

The failures appear unrelated to my commit

---

## Review 43 [Commented]

> Username: ashtum  
> Created_at: 2023-12-27 14:11:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2718#pullrequestreview-1797225068  

📁 test/beast/websocket/ping.cpp

```diff
 129 |+             test::run_for(ioc_, std::chrono::milliseconds(750));
 130 |+             // At 2.5s total; should have triggered the idle timer
 131 |+             BEAST_EXPECT(ws.impl_->idle_counter == 1);
```

> Username: ashtum  
> Created_at: 2023-12-27 14:11:11 UTC  
> Updated_at: 2023-12-27 14:11:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#discussion_r1437066289  

I believe the tight timing is a consequence of asserting `idle_counter` in multiple places. There is a good chance that these timings could lead to non-deterministic test results. Therefore, I suggest not checking for `idle_counter` and instead only verifying the side effect (inactivity timeout doesn't occur when receiving pings).


---

## Comment 44

> Username: ashtum  
> Created_at: 2024-02-15 07:06:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1945479262  

@xim, I've removed the `idle_counter` assertions in the hope that this will reduce the chance of getting non-deterministic test results. Let's see if we can run CI multiple times with all tests passing.

---

## Comment 45

> Username: cppalliance-bot  
> Created_at: 2024-02-15 07:13:22 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1945485971  

An automated preview of the documentation is available at [https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 46

> Username: cppalliance-bot  
> Created_at: 2024-02-15 09:53:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1945728066  

An automated preview of the documentation is available at [https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 47

> Username: cppalliance-bot  
> Created_at: 2024-02-15 11:58:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1945954888  

An automated preview of the documentation is available at [https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 48

> Username: cppalliance-bot  
> Created_at: 2024-02-16 18:33:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1949078429  

An automated preview of the documentation is available at [https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 49

> Username: cppalliance-bot  
> Created_at: 2024-02-16 19:08:22 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1949144078  

An automated preview of the documentation is available at [https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 50

> Username: cppalliance-bot  
> Created_at: 2024-02-19 12:43:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-1952373774  

An automated preview of the documentation is available at [https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2718.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Review 51 [Commented]

> Username: ashtum  
> Created_at: 2024-02-19 14:26:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2718#pullrequestreview-1888540586  

📁 include/boost/beast/websocket/impl/read.hpp

```diff
 228 |                     if(impl.rd_fh.op == detail::opcode::ping)
 229 |                     {
 230 |-                         impl.update_timer(this->get_executor());
```

> Username: ashtum  
> Created_at: 2024-02-19 14:26:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#discussion_r1494627227  

Updating the timer is necessary; otherwise, it will attempt to ping the other side even after receiving a ping.


---

## Comment 52

> Username: sdarwin  
> Created_at: 2024-10-21 22:06:49 UTC  
> Updated_at: 2024-10-21 22:44:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2718#issuecomment-2427829097  

Testing out a new docs servers, there may be another "automated preview" shortly on this pull request.  
  
Update: this pull request is over 1 year old.  There is some issue with the file contents.  Not critical.  You could rebase on `develop` to update all files.

---
