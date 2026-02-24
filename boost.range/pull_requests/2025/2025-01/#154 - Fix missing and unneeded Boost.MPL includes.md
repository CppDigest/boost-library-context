# #154 Fix missing and unneeded Boost.MPL includes [Merged]

> Username: Lastique  
> Created at: 2025-01-31 23:14:54 UTC  
> Updated at: 2025-02-08 12:14:00 UTC  
> Merged at: 2025-02-07 10:57:30 UTC  
> Closed at: 2025-02-07 10:57:30 UTC  
> Url: https://github.com/boostorg/range/pull/154  

This adds missing Boost.MPL includes, as well as removes includes that are not used. It also updates some of the Boost.Core includes to use up to date headers instead of the deprecated forwarding stubs.

---

## Comment 1

> Username: Lastique  
> Created_at: 2025-02-07 10:52:37 UTC  
> Url: https://github.com/boostorg/range/pull/154#issuecomment-2642580549  

Gentle ping. Boost.Range will now fail to compile because of the missing includes, as Boost.MPL headers are no longer included by Boost.Iterator.

---

## Comment 2

> Username: neilgroves  
> Created_at: 2025-02-07 10:58:32 UTC  
> Url: https://github.com/boostorg/range/pull/154#issuecomment-2642592226  

On Fri, 7 Feb 2025 at 10:53, Andrey Semashev ***@***.***>  
wrote:  
  
> Gentle ping. Boost.Range will now fail to compile because of the missing  
> includes, as Boost.MPL headers are no longer included by Boost.Iterator.  
>  
  
Done. Sorry for the delay.  
  
  
  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/range/pull/154#issuecomment-2642580549>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABTIJ2TWZDHUTQDBM4LIJRD2OSGAZAVCNFSM6AAAAABWISOF5WVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDMNBSGU4DANJUHE>  
> .  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>

---

## Comment 3

> Username: Lastique  
> Created_at: 2025-02-08 12:13:59 UTC  
> Url: https://github.com/boostorg/range/pull/154#issuecomment-2645296219  

Please, also merge to master. Boost.Iterator has been merged.

---
