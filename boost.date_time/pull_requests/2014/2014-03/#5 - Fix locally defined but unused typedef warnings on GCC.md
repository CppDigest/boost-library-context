# #5 Fix locally defined but unused typedef warnings on GCC. [Closed]

> Username: Lastique  
> Created at: 2014-03-01 13:26:50 UTC  
> Updated at: 2014-08-04 09:17:41 UTC  
> Closed at: 2014-03-02 09:34:18 UTC  
> Url: https://github.com/boostorg/date_time/pull/5  

The commit removes unused typedefs that cause warnings on recent GCC versions. These warnings often propagate to other libraries which use Boost.DateTime.

---

## Comment 1

> Username: mclow  
> Created_at: 2014-03-01 19:37:50 UTC  
> Url: https://github.com/boostorg/date_time/pull/5#issuecomment-36434228  

Andrey - can you send this pull request to boost-maint@lists.boost.org, please?  
More people will see it there.

---

## Comment 2

> Username: ahmedcharles  
> Created_at: 2014-03-02 09:34:18 UTC  
> Url: https://github.com/boostorg/date_time/pull/5#issuecomment-36450033  

I pushed this as: https://github.com/boostorg/date_time/commit/f42e7f62873bca0f1b1fa44722c202f6a6fa6b6e  
  
After testing.

---
