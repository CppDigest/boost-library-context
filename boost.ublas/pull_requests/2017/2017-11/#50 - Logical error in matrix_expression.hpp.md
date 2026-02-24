# #50 Logical error in matrix_expression.hpp? [Closed]

> Username: iljah  
> Created at: 2017-11-13 10:23:48 UTC  
> Updated at: 2019-04-27 10:58:27 UTC  
> Closed at: 2019-04-21 19:16:56 UTC  
> Url: https://github.com/boostorg/ublas/pull/50  

GCC 7.1.0 warned about misleading indentation...

---

## Comment 1

> Username: austin-beer  
> Created_at: 2018-11-06 00:30:42 UTC  
> Url: https://github.com/boostorg/ublas/pull/50#issuecomment-436087392  

I'm encountering this same warning as well with GCC 8.2.0, and I believe this PR would eliminate the warning.

---

## Comment 2

> Username: austin-beer  
> Created_at: 2019-04-19 19:18:59 UTC  
> Url: https://github.com/boostorg/ublas/pull/50#issuecomment-484991152  

This issue was fixed in 53361d7f42e7d88661c147a2490e10ee32324e48 and so this PR can be closed.

---

## Comment 3

> Username: bassoy  
> Created_at: 2019-04-21 19:17:25 UTC  
> Url: https://github.com/boostorg/ublas/pull/50#issuecomment-485275680  

Thanks.  
  
Am Fr., 19. Apr. 2019 um 21:19 Uhr schrieb Austin Beer <  
notifications@github.com>:  
  
> This issue was fixed in 53361d7  
> <https://github.com/boostorg/ublas/commit/53361d7f42e7d88661c147a2490e10ee32324e48>  
> and so this PR can be closed.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/ublas/pull/50#issuecomment-484991152>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AA6PDJNWP6NDVBGXRFMTBM3PRILKJANCNFSM4EDOE4PA>  
> .  
>

---
