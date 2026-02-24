# #533 Fix cygwin build. [Open]

> Username: corngood  
> Created at: 2025-12-18 14:40:27 UTC  
> Updated at: 2026-02-11 02:50:29 UTC  
> Url: https://github.com/boostorg/process/pull/533  

This is part of a set of changes to fix boost on cygwin.  You can see the other submodules that will require changes here:  
  
https://github.com/boostorg/boost/compare/master...corngood:boost:cygwin  
  
These are based on the out-of-date downstream patches in cygwin:  
  
https://cygwin.com/cgit/cygwin-packages/boost/tree/

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2025-12-18 14:46:31 UTC  
> Url: https://github.com/boostorg/process/pull/533#issuecomment-3670660463  

Thanks!  
Are asio & system already patched?

---

## Comment 2

> Username: corngood  
> Created_at: 2025-12-18 14:51:20 UTC  
> Url: https://github.com/boostorg/process/pull/533#issuecomment-3670682204  

I just created PRs for all of the submodules in https://github.com/boostorg/boost/compare/master...corngood:boost:cygwin at the same time.  
  
I'm not quite sure how to manage getting them all merged.  Should I make a tracking issue on boostorg/boost?

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2025-12-18 15:14:46 UTC  
> Url: https://github.com/boostorg/process/pull/533#issuecomment-3670797425  

You probably should go to the boost mailing lists with this one.

---

## Comment 4

> Username: corngood  
> Created_at: 2025-12-18 15:37:29 UTC  
> Url: https://github.com/boostorg/process/pull/533#issuecomment-3670889755  

Do you mean write an intro with links to the PRs to the mailing list?  It doesn't look like patches are generally sent there.

---
