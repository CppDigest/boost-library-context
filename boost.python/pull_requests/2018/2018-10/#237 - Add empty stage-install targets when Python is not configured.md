# #237 Add empty stage/install targets when Python is not configured [Merged]

> Username: pdimov  
> Created at: 2018-10-12 18:18:45 UTC  
> Updated at: 2019-01-10 03:59:59 UTC  
> Merged at: 2018-10-12 18:34:16 UTC  
> Closed at: 2018-10-12 18:34:16 UTC  
> Url: https://github.com/boostorg/python/pull/237  

Since the previously added boost-install is in `if [ python.configured ]`, this `else` adds stage/install targets (that issue the preexisting warning message) in case Python hasn't been configured.

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-10-12 18:21:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/237#pullrequestreview-164357066  

Could you please add a comment to indicate the purpose of this target ? Otherwise I'm afraid I'll forget over time. Thanks !

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-10-12 18:32:37 UTC  
> Url: https://github.com/boostorg/python/pull/237#issuecomment-429418906  

Done.

---

## Review 3 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-10-12 18:33:53 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/python/pull/237#pullrequestreview-164361628  

Many thanks !

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-01-10 03:59:59 UTC  
> Url: https://github.com/boostorg/python/pull/237#issuecomment-452963081  

Can we please have this, along with https://github.com/boostorg/python/pull/236, merged to master?

---
