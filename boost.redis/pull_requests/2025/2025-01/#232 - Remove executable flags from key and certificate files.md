# #232 Remove executable flags from key and certificate files [Merged]

> Username: striezel  
> Created at: 2025-01-27 10:14:00 UTC  
> Updated at: 2025-02-08 19:39:21 UTC  
> Merged at: 2025-02-08 19:39:20 UTC  
> Closed at: 2025-02-08 19:39:20 UTC  
> Url: https://github.com/boostorg/redis/pull/232  

The certificate and key files should not have the executable flag set.  
  
See <https://github.com/boostorg/admin/issues/47#issuecomment-2575165830>.

---

## Comment 1

> Username: mzimbres  
> Created_at: 2025-02-02 21:38:24 UTC  
> Url: https://github.com/boostorg/redis/pull/232#issuecomment-2629565650  

@striezel Thanks for the PR. I think these files were added in a PR from @anarthal so I am requesting his review as well.

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-02-02 22:18:01 UTC  
> Url: https://github.com/boostorg/redis/pull/232#issuecomment-2629582645  

Looks good to me. I merged the equivalent PR in MySQL and everything was good.

---
