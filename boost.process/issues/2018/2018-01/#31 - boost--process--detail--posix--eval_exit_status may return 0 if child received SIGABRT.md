# #31 - boost::process::detail::posix::eval_exit_status may return 0 if child received SIGABRT [Closed]

> Username: hrayrbabajanyan  
> Created at: 2018-01-31 22:01:21 UTC  
> Updated at: 2018-02-06 10:28:27 UTC  
> Closed at: 2018-02-05 22:17:03 UTC  
> Url: https://github.com/boostorg/process/issues/31  

In case if child received SIGABRT WIFEXITED() may be false and WEXITSTATUS() will result in 0.  
  
proposed change:  
```  
// from  
inline int eval_exit_status(int code)  
{  
    return WEXITSTATUS(code);  
}  
// to  
inline int eval_exit_status(int code)  
{  
        if (WIFEXITED(code))  
        {  
                return WEXITSTATUS(code);  
        }  
        else if (WIFSIGNALED(code))  
        {  
                return WTERMSIG(code);  
        }  
        else if (WIFSTOPPED(code))  
        {  
                return WSTOPSIG(code);  
        }  
        else  
        {  
                return code;  
        }  
}  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-01-31 22:28:07 UTC  
> Url: https://github.com/boostorg/process/issues/31#issuecomment-362092464  

Thanks for reporting, that would make sense. I'll have to check if that would make it incompatible with something on windows first though.

---

## Comment 2

> Username: hrayrbabajanyan  
> Created at: 2018-01-31 22:37:12 UTC  
> Url: https://github.com/boostorg/process/issues/31#issuecomment-362094756  

Hi Klemens,  
  
I copied the code from my own process library that I am replacing with  
boost.process. The suggested code make exit codes compatible with windows  
and *nix shell.  
Please review it. And should you have questions please don't hesitate to  
ask.  
  
Regards,  
Hrayr  
  
--  
Hrayr BABAJANYAN  
e-mail: hrayr.babajanyan@gmail.com  
   url: www.babajanyan.com  
mobile: +374 93 747297  
office: +374 60 448471  
   fax: +374 10 542927  
  
On Thu, Feb 1, 2018 at 2:28 AM, Klemens Morgenstern <  
notifications@github.com> wrote:  
  
> Thanks for reporting, that would make sense. I'll have to check if that  
> would make it incompatible with something on windows first though.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/process/issues/31#issuecomment-362092464>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AiWM11ARDcUOpXdnjoUlFzMG7BtC9XiLks5tQOj3gaJpZM4R0t-J>  
> .  
>

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2018-01-31 22:40:30 UTC  
> Url: https://github.com/boostorg/process/issues/31#issuecomment-362095605  

If you create a PR (against www.github.com/klemens-morgenstern/boost-process) and the CI runs through I'll merge it, otherwise I'll do that myself on the weekend. I have a bunch of open issues in the library that I should address soon.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2018-02-05 22:17:03 UTC  
> Url: https://github.com/boostorg/process/issues/31#issuecomment-363240187  

Since it's merged, I'm closing the PR. Let me know if you find anything else and thanks!

---

## Comment 5

> Username: hrayrbabajanyan  
> Created at: 2018-02-06 10:28:27 UTC  
> Url: https://github.com/boostorg/process/issues/31#issuecomment-363379431  

Thanks!  
  
--  
Hrayr BABAJANYAN  
e-mail: hrayr.babajanyan@gmail.com  
   url: www.babajanyan.com  
mobile: +374 93 747297  
office: +374 60 448471  
   fax: +374 10 542927  
  
On Tue, Feb 6, 2018 at 2:17 AM, Klemens Morgenstern <  
notifications@github.com> wrote:  
  
> Closed #31 <https://github.com/boostorg/process/issues/31>.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/process/issues/31#event-1458957173>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AiWM1whwt4MxZEDPD8S2JiR-gz0qAOG9ks5tR33ggaJpZM4R0t-J>  
> .  
>
