# #2331 Allow test::stream to be used with nortti [Closed]

> Username: edtanous  
> Created at: 2021-10-21 21:32:47 UTC  
> Updated at: 2021-11-03 21:42:42 UTC  
> Closed at: 2021-11-03 21:42:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2331  

Currently, the test stream code doesn't compile when both debug builds  
(namely assertions) are enabled along with disabling rtti.  The current  
code directly uses type_id(), which isn't available without rtti.  A  
quick search through the asio codebase came up with a couple possible  
solutions.  The first is here:  
https://github.com/boostorg/asio/blob/97d8a5686e0eed8e53b77f3c225c328a812ac833/include/boost/asio/detail/handler_work.hpp#L225  
  
Which essentially wraps the check in an  
  
This is possible, but seems like it would lose the value of the  
assertion.  
  
The second possible solution is here:  
https://github.com/boostorg/asio/blob/c77f3b603bf2cd180945184a664c6a05d3bebd3b/include/boost/asio/impl/executor.hpp#L282  
  
It seems that asio::executor has created its own template-based type_id  
class that is able to switch on BOOST_ASIO_NO_TYPEID, and fallback to a  
void* for type_id_result_type.  
https://github.com/boostorg/asio/blob/c77f3b603bf2cd180945184a664c6a05d3bebd3b/include/boost/asio/executor.hpp#L271  
  
This patch currently opts for the second solution, pulling in  
executor.hpp, and uses its implementation.  This solves the error, and  
allows the test stream to compile with -fnortti set.  Given my limited  
knowledge of executors, it's not clear if putting a hard dependency on  
executor.hpp in beast is desired or recommended here.  
  
Signed-off-by: Ed Tanous <ed@tanous.net>

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-10-21 22:27:53 UTC  
> Updated_at: 2021-10-25 17:42:45 UTC  
> Url: https://github.com/boostorg/beast/pull/2331#issuecomment-949049240  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2331?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2331](https://codecov.io/gh/boostorg/beast/pull/2331?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b45f01a) into [develop](https://codecov.io/gh/boostorg/beast/commit/b15a5ff0e47e72ba3d4711d2514bc65749d036ae?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b15a5ff) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2331/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2331?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #2331   +/-   ##  
========================================  
  Coverage    94.88%   94.88%             
========================================  
  Files          151      151             
  Lines        12230    12230             
========================================  
  Hits         11605    11605             
  Misses         625      625             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2331?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2331?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b15a5ff...b45f01a](https://codecov.io/gh/boostorg/beast/pull/2331?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-10-21 23:02:28 UTC  
> Updated_at: 2021-10-21 23:03:11 UTC  
> Url: https://github.com/boostorg/beast/pull/2331#issuecomment-949063560  

Not ok, because `type_id()` is private. How about this instead:  
```  
BOOST_ASSERT( in_->exec.template target<Executor2>() != nullptr );  
```  
?

---

## Comment 3

> Username: edtanous  
> Created_at: 2021-10-25 16:41:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2331#issuecomment-951108051  

> Not ok, because `type_id()` is private. How about this instead:  
>   
  
So it is (wonders why code compiled on his machine).  
  
> ```  
> BOOST_ASSERT( in_->exec.template target<Executor2>() != nullptr );  
> ```  
  
That seems like a different check, checking to see if the target is null, versus checking to see if the two executor types are the same.  Or is there a subtlety I'm missing?  
  
Will update the patchset shortly.  
  
>   
> ?

---

## Comment 4

> Username: edtanous  
> Created_at: 2021-10-25 20:23:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2331#issuecomment-951287849  

The build failure looks like some common issue with CI getting rate limited?  
  
> fatal: unable to access 'https://github.com/boostorg/boost.git/': The requested URL returned error: 429

---

## Comment 5

> Username: madmongo1  
> Created_at: 2021-10-25 20:24:25 UTC  
> Url: https://github.com/boostorg/beast/pull/2331#issuecomment-951289613  

Yep, I’m not worried about that  
  
On Mon, 25 Oct 2021 at 22:23, Ed Tanous ***@***.***> wrote:  
  
> The build failure looks like some common issue with CI getting rate  
> limited?  
>  
> fatal: unable to access 'https://github.com/boostorg/boost.git/': The  
> requested URL returned error: 429  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/pull/2331#issuecomment-951287849>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSLHM63K6FWRHAYSTS3UIW4ELANCNFSM5GPDRE5A>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>  
>  
--   
Richard Hodges  
***@***.***  
office: +44 2032 898 513  
home: +376 861 195  
mobile: +376 380 212

---

## Comment 6

> Username: madmongo1  
> Created_at: 2021-11-03 14:56:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2331#issuecomment-959338544  

@vinniefalco I'm just running some final checks on this.  
It fixes use of the test::stream in a debug build when -fno-rtti is set.  
However, setting -fno-rtti causes the entire beast test suite to fail.  
Is it our intention to export test::stream as a class people can use in their code?

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2021-11-03 16:13:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2331#issuecomment-959597879  

> Is it our intention to export test::stream as a class people can use in their code?  
  
Yes test::stream is supposed to be public

---
