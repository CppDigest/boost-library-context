# #102 Adding OS variations for travis CI build [Closed]

> Username: renu555  
> Created at: 2015-11-24 06:20:40 UTC  
> Updated at: 2015-11-24 14:34:26 UTC  
> Closed at: 2015-11-24 14:34:26 UTC  
> Url: https://github.com/boostorg/boost/pull/102  

I get authentication error in Travis build. Can anyone please help ?  
Cloning into 'libs/accumulators'...  
remote: Invalid username or password.  
fatal: Authentication failed for 'https://github.com/renu555/accumulators.git/'  
Clone of 'https://github.com/renu555/accumulators.git' into submodule path 'libs/accumulators' failed  
The command "eval git submodule update" failed. Retrying, 2 of 3.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2015-11-24 14:34:26 UTC  
> Url: https://github.com/boostorg/boost/pull/102#issuecomment-159285128  

We don't do PRs into master branch. And I don't see the point in adding the variations since this doesn't actually build anything.

---
