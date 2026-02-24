# #240 Disable detailed warnings on CI builds [Merged]

> Username: mloskot  
> Created at: 2019-02-22 22:07:16 UTC  
> Updated at: 2019-03-29 20:27:05 UTC  
> Merged at: 2019-02-22 23:04:13 UTC  
> Closed at: 2019-02-22 23:04:13 UTC  
> Url: https://github.com/boostorg/gil/pull/240  

This should help to avoid failures on Travis CI (and possibly others):  
  
>  The job exceeded the maximum log length, and has been terminated.  
  
### Tasklist  
  
- [ ] All CI builds and checks have passed  
  
-----  
  
@stefanseefeld Having more and more tests, I'm afraid, we have no other choice. I also think it's more practical to observe and resolve those extra warnings locally, than based on CI logs. Let's stick to CI providing us with boolean green/red status

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2019-02-22 22:12:10 UTC  
> Url: https://github.com/boostorg/gil/pull/240#issuecomment-466566071  

@mloskot yes, I agree.

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-02-22 23:03:58 UTC  
> Url: https://github.com/boostorg/gil/pull/240#issuecomment-466579162  

The branch builds on my Travis CI: https://travis-ci.org/mloskot/gil/builds/497270642  
It also builds fine on AzP and AppVeyor.  
I'm merging without waiting long hours for the boostorg's Travis CI.

---
