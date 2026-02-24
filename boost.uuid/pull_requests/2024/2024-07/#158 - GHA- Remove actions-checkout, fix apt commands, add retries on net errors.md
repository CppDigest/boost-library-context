# #158 GHA: Remove actions/checkout, fix apt commands, add retries on net errors [Closed]

> Username: Lastique  
> Created at: 2024-07-04 11:24:11 UTC  
> Updated at: 2025-07-03 22:27:47 UTC  
> Closed at: 2025-07-03 22:02:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/158  

GitHub actions/checkout@v3 is no longer working and v4 is incompatible with older Linux versions due to https://github.com/actions/checkout/issues/1590. To fix this, and permanently eliminate the issue of periodic deprecation of actions/checkout, replace it with manual downloads of git snapshots using curl.  
  
Additionally, fixed apt command lines that would incorrectly expand the list of packages to install. Added options to retry on network errors to reduce the probability of spurious CI failures. Also added git checkout parallel jobs to potentially speed up the checkout.  
  
This should fix CI failures with actions/checkout@v3 and eliminate GHA deprecation warnings.

---

## Comment 1

> Username: jeking3  
> Created_at: 2024-11-12 12:07:06 UTC  
> Url: https://github.com/boostorg/uuid/pull/158#issuecomment-2470360997  

Don't they have an actions@current so you can stay current and risk having a build break the other way?  Seems less likely; I would be surprised if they do not.

---

## Comment 2

> Username: Lastique  
> Created_at: 2024-11-12 14:11:19 UTC  
> Url: https://github.com/boostorg/uuid/pull/158#issuecomment-2470641549  

`actions@current` means their upgrades will randomly break CI - when they do the upgrade rather than when our maintainer does. Either way, the breakage takes place.

---

## Comment 3

> Username: Lastique  
> Created_at: 2024-11-12 14:20:55 UTC  
> Url: https://github.com/boostorg/uuid/pull/158#issuecomment-2470665829  

@pdimov @jeking3 Is this PR not going to be accepted? Do I need to update it or it can be just closed?

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-11-12 14:28:11 UTC  
> Url: https://github.com/boostorg/uuid/pull/158#issuecomment-2470684767  

There's no need for it at the moment, but we might need it in the future when GHA breaks `actions/checkout` entirely on old images.

---

## Comment 5

> Username: jeking3  
> Created_at: 2025-07-03 20:54:51 UTC  
> Url: https://github.com/boostorg/uuid/pull/158#issuecomment-3033621428  

I'd recommend moving to use the new reusable workflows in Boost.CI which reduces the effort on keeping CI running in GHA.  Most of the CMT repos and boost/format have moved to this in develop.

---

## Comment 6

> Username: Lastique  
> Created_at: 2025-07-03 22:02:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/158#issuecomment-3033796679  

Closing due to lack of interest.

---
