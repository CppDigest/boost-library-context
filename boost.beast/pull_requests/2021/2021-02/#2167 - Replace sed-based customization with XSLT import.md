# #2167 Replace sed-based customization with XSLT import: [Merged]

> Username: evanlenz  
> Created at: 2021-02-18 00:45:30 UTC  
> Updated at: 2021-02-25 18:56:50 UTC  
> Merged at: 2021-02-25 18:56:49 UTC  
> Closed at: 2021-02-25 18:56:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2167  

fix boostorg/docca#40  
  
This must be updated in parallel with the corresponding change in boostorg/docca. Otherwise, the doc build will fail.

---

## Review 1 [Commented]

> Username: madmongo1  
> Created_at: 2021-02-18 08:56:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2167#pullrequestreview-592980644  

How do I get this to run through CI without a failure?  
Is the docca develop branch updated?

---

## Comment 2

> Username: evanlenz  
> Created_at: 2021-02-18 17:06:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2167#issuecomment-781494094  

It indeed depends on the corresponding pull request in docca. So we would  
need to review and approve that one first. This is a one-time  
compatibility-breaking change; I have also updated Boost.JSON’s doc files  
in the same way.  
  
On Thu, Feb 18, 2021 at 12:57 AM Richard Hodges <notifications@github.com>  
wrote:  
  
> *@madmongo1* commented on this pull request.  
>  
> How do I get this to run through CI without a failure?  
> Is the docca develop branch updated?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/pull/2167#pullrequestreview-592980644>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAETWLVS5Z4AAH233LYSXPLS7TI6LANCNFSM4XZK75YQ>  
> .  
>

---

## Comment 3

> Username: madmongo1  
> Created_at: 2021-02-18 17:54:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2167#issuecomment-781526341  

OK, let me know when you want to update docca and I'll get this merged

---
