# #25 Enables the timer scheduler's customization for both deadline and waitable timers [Open]

> Username: securesocketfunneling  
> Created at: 2015-09-18 09:16:12 UTC  
> Updated at: 2015-09-23 12:58:42 UTC  
> Url: https://github.com/boostorg/asio/pull/25  

This pull request is linked to pull request discussion #24  
  
The discussion on timer precisions with @haegele-tv lead us to think that we have to design our own timer mechanism for the [boost asio udt](https://github.com/securesocketfunneling/udt) implementation requirements.  
  
The perfect place to do so would be to design a specific timer scheduler (as it is done with WinRT, or Linux, Windows...) and benefit of the entire deadline/waitable timer io_objects and io_services architecture.  
  
Yet, the timer scheduler is NOT customizable by the user. This patch allows third party developers to specify a custom timer scheduler for any specific requirements.  
  
What do you think?

---

## Comment 1

> Username: securesocketfunneling  
> Created_at: 2015-09-23 12:58:42 UTC  
> Url: https://github.com/boostorg/asio/pull/25#issuecomment-142591259  

Here is a branch using custom timer_scheduler:  
  
[Timer_benchmark tool](https://github.com/securesocketfunneling/udt/tree/develop-high-precision-timer-scheduler/src/timer_benchmark) on `develop-high-precision-timer-scheduler` branch.  
  
This could be achieved using timer_scheduler customization as this PR suggests.

---
