# #580 Add missing return to svm_ptr assignment operator [Merged]

> Username: kylelutz  
> Created at: 2016-04-07 14:59:17 UTC  
> Updated at: 2016-04-07 22:30:43 UTC  
> Merged at: 2016-04-07 22:10:42 UTC  
> Closed at: 2016-04-07 22:10:42 UTC  
> Url: https://github.com/boostorg/compute/pull/580  



---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-04-07 22:19:55 UTC  
> Url: https://github.com/boostorg/compute/pull/580#issuecomment-207113411  

I wonder if we need to find a stable POCL version (commit), use it instead of master branch because it became too unstable and just upgrade it from time to time after testing that new version is stable.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-04-07 22:21:53 UTC  
> Url: https://github.com/boostorg/compute/pull/580#issuecomment-207113832  

Yeah, I think that would be better and make the CI results more dependable. Would it make sense to stick to POCL releases or other tagged versions?

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-04-07 22:28:54 UTC  
> Updated_at: 2016-04-07 22:30:43 UTC  
> Url: https://github.com/boostorg/compute/pull/580#issuecomment-207117379  

I'm not sure about releases (0.12 is 600 commits behind master,  0.13 - 200 commits behind) I will have to test them. If 0.13 works I'll use it, otherwise I'll just figure out the latest commit that works. I'll do this on Saturday.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2016-04-07 22:30:28 UTC  
> Url: https://github.com/boostorg/compute/pull/580#issuecomment-207118342  

Sounds good to me, thanks!

---
