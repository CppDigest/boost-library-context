# #24 cleanup pending and detail headers [Merged]

> Username: jeking3  
> Created at: 2018-08-21 03:30:25 UTC  
> Updated at: 2018-10-21 16:44:21 UTC  
> Merged at: 2018-10-21 16:44:13 UTC  
> Closed at: 2018-10-21 16:44:13 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/24  

Folded in pending and detail headers so they are more directly associated with dynamic_bitset.  A scan of all headers across boost shows no external usage of lowest_bit (the only header that includes it is dynamic_bitset.hpp), and detail can be moved freely.  Moved the dynamic_bitset detail out of the top level boost/detail header directory.

---

## Review 1 [Commented]

> Username: MarcelRaad  
> Created_at: 2018-08-21 03:41:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/24#pullrequestreview-147895347  

📁 include/boost/dynamic_bitset/dynamic_bitset.hpp

```diff
1348 | 
1349 |+ template <typename T>
1350 |+ int lowest_bit(T x) {
```

> Username: MarcelRaad  
> Created_at: 2018-08-21 03:41:06 UTC  
> Updated_at: 2018-10-12 12:00:36 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/24#discussion_r211470232  

Did you mean to duplicate lowest_bit, in its own header and here?


---

## Comment 2

> Username: jeking3  
> Created_at: 2018-08-21 10:58:11 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/24#issuecomment-414635325  

Thanks for catching that.  I tried that first then decided just to move the  
file.  I will fix that up.  
  
On Mon, Aug 20, 2018, 11:41 PM Marcel Raad <notifications@github.com> wrote:  
  
> *@MarcelRaad* commented on this pull request.  
> ------------------------------  
>  
> In include/boost/dynamic_bitset/dynamic_bitset.hpp  
> <https://github.com/boostorg/dynamic_bitset/pull/24#discussion_r211470232>  
> :  
>  
> > @@ -1346,6 +1346,17 @@ bool dynamic_bitset<Block, Allocator>::intersects(const dynamic_bitset & b) cons  
>  // --------------------------------  
>  // lookup  
>  
> +template <typename T>  
> +int lowest_bit(T x) {  
>  
> Did you mean to duplicate lowest_bit, in its own header and here?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/dynamic_bitset/pull/24#pullrequestreview-147895347>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ALOdbeHieCs7hatwtX0M20J6ASdNm15bks5uS4FSgaJpZM4WFC5Y>  
> .  
>

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2018-08-21 11:55:51 UTC  
> Updated_at: 2018-10-12 15:25:08 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/24#issuecomment-414648452  

# [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/24?src=pr&el=h1) Report  
> Merging [#24](https://codecov.io/gh/boostorg/dynamic_bitset/pull/24?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/dynamic_bitset/commit/a0735943f22c4cc39c740b406c96c5b65f2814fb?src=pr&el=desc) will **increase** coverage by `1.29%`.  
> The diff coverage is `50%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/dynamic_bitset/pull/24/graphs/tree.svg?width=650&token=PVG5jth1ez&height=150&src=pr)](https://codecov.io/gh/boostorg/dynamic_bitset/pull/24?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #24      +/-   ##  
===========================================  
+ Coverage    75.86%   77.15%   +1.29%       
===========================================  
  Files            5        5                
  Lines          551      604      +53       
  Branches       199      211      +12       
===========================================  
+ Hits           418      466      +48       
- Misses          23       24       +1       
- Partials       110      114       +4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/dynamic_bitset/pull/24?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...ude/boost/dynamic\_bitset/detail/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/24/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9kZXRhaWwvZHluYW1pY19iaXRzZXQuaHBw) | `93.54% <ø> (ø)` | |  
| [include/boost/dynamic\_bitset/detail/lowest\_bit.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/24/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9kZXRhaWwvbG93ZXN0X2JpdC5ocHA=) | `66.66% <0%> (ø)` | |  
| [include/boost/dynamic\_bitset/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/24/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9keW5hbWljX2JpdHNldC5ocHA=) | `76.24% <100%> (+1.48%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/24?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/24?src=pr&el=footer). Last update [a073594...2d24bdd](https://codecov.io/gh/boostorg/dynamic_bitset/pull/24?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-08-22 13:07:37 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/24#issuecomment-415025242  

Apart from the coverage result oddity, this is ready for another review.  I did change the assertion to an exception to make it testable.

---

## Comment 5

> Username: swatanabe  
> Created_at: 2018-08-22 14:44:14 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/24#issuecomment-415057491  

AMDG  
  
On 08/22/2018 07:07 AM, James E. King III wrote:  
> I did change the assertion to an exception to make it testable.  
>   
  
  Please don't.  It's an assertion, because it's a precondition.  
The behavior when violating a precondition is untestable because  
it's undefined.  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-08-29 02:26:15 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/24#issuecomment-416802329  

Fair enough - although assertions can be tested if the test harness can be told to expect a core.

---

## Review 7 [Approved]

> Username: Kojoley  
> Created_at: 2018-09-02 13:37:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/24#pullrequestreview-151621359  

📁 include/boost/dynamic_bitset/detail/lowest_bit.hpp

```diff
  25 | 
  26 |-         assert(x >= 1); // PRE
  26 |+ 	BOOST_ASSERT(x >= 1); // PRE
```

> Username: Kojoley  
> Created_at: 2018-09-02 13:37:19 UTC  
> Updated_at: 2018-10-12 12:00:36 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/24#discussion_r214541163  

You have tabs in several places

> Username: jeking3  
> Created_at: 2018-09-04 15:54:17 UTC  
> Updated_at: 2018-10-12 12:00:36 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/24#discussion_r214971156  

Thanks, I will fix that up.


---

## Comment 8

> Username: pdimov  
> Created_at: 2018-10-12 13:09:05 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/24#issuecomment-429319674  

Yes, no turning of assertions into exceptions please. :-)  
  
If you insist on testing the BOOST_ASSERT, you can use BOOST_ENABLE_ASSERT_HANDLER, although precondition violation testing is a somewhat dubious practice.

---
