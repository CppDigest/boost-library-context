# #250 Remove missing test from Jamfile. [Merged]

> Username: jeremy7nelson  
> Created at: 2019-09-06 23:39:55 UTC  
> Updated at: 2019-09-08 13:15:46 UTC  
> Merged at: 2019-09-07 12:00:51 UTC  
> Closed at: 2019-09-07 12:00:51 UTC  
> Url: https://github.com/boostorg/math/pull/250  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-09-07 08:13:13 UTC  
> Url: https://github.com/boostorg/math/pull/250#issuecomment-529085423  

@NAThompson are you able to add the missing file ASAP?  If not I'll merge this later as it blocks the regression testers from running.

---

## Comment 2

> Username: jeremy7nelson  
> Created_at: 2019-09-07 09:13:53 UTC  
> Url: https://github.com/boostorg/math/pull/250#issuecomment-529089780  

The file was renamed. So the old file should be removed.  
  
Sent from my iPhone  
  
On Sep 7, 2019, at 1:13 AM, jzmaddock <notifications@github.com> wrote:  
  
@NAThompson <https://github.com/NAThompson> are you able to add the missing  
file ASAP? If not I'll merge this later as it blocks the regression testers  
from running.  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub  
<https://github.com/boostorg/math/pull/250?email_source=notifications&email_token=AEAM2VZWGDBJJDNC6MUEMW3QINPBXA5CNFSM4IUN7UA2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOD6ETH3Y#issuecomment-529085423>,  
or mute the thread  
<https://github.com/notifications/unsubscribe-auth/AEAM2V3UQTCUA7U4EDRE2DLQINPBXANCNFSM4IUN7UAQ>  
.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2019-09-07 12:00:47 UTC  
> Url: https://github.com/boostorg/math/pull/250#issuecomment-529101974  

@jzmaddock : My bad; merging.  
  
@jeremy7nelson : Thanks for the fix!

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2019-09-07 12:12:21 UTC  
> Url: https://github.com/boostorg/math/pull/250#issuecomment-529102835  

Actually the test should have been renamed in the Jamfile rather than removed - just pushed.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2019-09-07 13:06:00 UTC  
> Url: https://github.com/boostorg/math/pull/250#issuecomment-529106664  

@jzmaddock : Disappointing lack of discipline here on my part.  
  
@jeremy7nelson : Just curious, are you consuming develop in your own projects, or using release branches?

---

## Comment 6

> Username: jeremy7nelson  
> Created_at: 2019-09-07 23:33:28 UTC  
> Url: https://github.com/boostorg/math/pull/250#issuecomment-529155003  

I used to use boost releases in my C++ code, but since the functionality I need is now part of std I don't need it anymore. Running the regression tests as a side project is one way I can give back. I only run tests on develop because my master results don't show on the website. Does anyone know how to get results for master reported?

---

## Comment 7

> Username: jeremy7nelson  
> Created_at: 2019-09-07 23:39:48 UTC  
> Url: https://github.com/boostorg/math/pull/250#issuecomment-529155265  

It was a little confusing. The test was renamed but the old test was not removed from the Jamfile (although it was removed from the example Jamfile, LOL).

---

## Comment 8

> Username: NAThompson  
> Created_at: 2019-09-08 13:15:46 UTC  
> Url: https://github.com/boostorg/math/pull/250#issuecomment-529201467  

Well, it looks like we need the extra pair of eyes; thanks!

---
