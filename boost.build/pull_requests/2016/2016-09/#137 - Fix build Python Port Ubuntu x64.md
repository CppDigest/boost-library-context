# #137 Fix build Python Port Ubuntu x64 [Closed]

> Username: pauloalves86  
> Created at: 2016-09-29 18:48:39 UTC  
> Updated at: 2021-10-02 20:59:27 UTC  
> Closed at: 2021-06-11 01:54:34 UTC  
> Url: https://github.com/boostorg/build/pull/137  

The system was not able to find the libpython on my system, and to fix that, it must search in another directory.

---

## Review 1 [Changes requested]

> Username: stefanseefeld  
> Created_at: 2016-11-01 19:51:47 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/137#pullrequestreview-6690684  

And similarly, on Fedora x64 I needed to add 'lib64'.

---

## Comment 2

> Username: vprus  
> Created_at: 2016-11-01 20:19:11 UTC  
> Url: https://github.com/boostorg/build/pull/137#issuecomment-257682965  

I actually have a wip patch to use pkg-config. Should be able to finish  
tomorrow  
  
On Tue, Nov 1, 2016, 22:51 Stefan Seefeld notifications@github.com wrote:  
  
> _@stefanseefeld_ requested changes on this pull request.  
>   
> And similarly, on Fedora x64 I needed to add 'lib64'.  
>   
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> https://github.com/boostorg/build/pull/137#pullrequestreview-6690684,  
> or mute the thread  
> https://github.com/notifications/unsubscribe-auth/AANyezvIktHtmWCRU9fnfBNrhjcpaRuuks5q55hUgaJpZM4KKV8M  
> .  
>   
> ##   
>   
> Vladimir Prus  
> http://vladimirprus.com

---

## Comment 3

> Username: stale[bot]  
> Created_at: 2021-05-29 16:21:54 UTC  
> Url: https://github.com/boostorg/build/pull/137#issuecomment-850859510  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 4

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:27 UTC  
> Url: https://github.com/boostorg/build/pull/137#issuecomment-932819648  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
