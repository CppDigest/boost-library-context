# #1 Supressed 'unused parameters' warnings. [Closed]

> Username: povilasb  
> Created at: 2014-06-08 15:35:43 UTC  
> Updated at: 2015-07-21 10:52:12 UTC  
> Closed at: 2015-07-19 17:08:58 UTC  
> Url: https://github.com/boostorg/parameter/pull/1  



---

## Comment 1

> Username: danieljames  
> Created_at: 2014-06-08 16:45:03 UTC  
> Url: https://github.com/boostorg/parameter/pull/1#issuecomment-45441614  

I think this is already addressed in develop in:  
  
https://github.com/boostorg/parameter/commit/b5e57e0024ae51f5e706eff50e860231826e8ed8  
  
I want to merge it to master pretty soon.

---

## Comment 2

> Username: eldiener  
> Created_at: 2015-05-28 22:31:19 UTC  
> Url: https://github.com/boostorg/parameter/pull/1#issuecomment-106618506  

You should not be making PRs on the 'master' branch. If the fix is already in 'develop' it will get into 'master' when 'develop' is merged to 'master'.

---

## Comment 3

> Username: eldiener  
> Created_at: 2015-05-28 22:43:10 UTC  
> Url: https://github.com/boostorg/parameter/pull/1#issuecomment-106620055  

My error. I see where the 'develop' and the 'master' branch have not been synced for a long time so I understand why this PR was made.

---

## Comment 4

> Username: eldiener  
> Created_at: 2015-07-19 17:08:58 UTC  
> Url: https://github.com/boostorg/parameter/pull/1#issuecomment-122683273  

I manually merged the "suppress 'unused parameter' warning/error" portion of your PR. The removing of trailing white spaces is not necessary for correct C++ compilation.

---

## Comment 5

> Username: povilasb  
> Created_at: 2015-07-21 10:52:12 UTC  
> Url: https://github.com/boostorg/parameter/pull/1#issuecomment-123261205  

Well trailing white space removal is for code neatness. They are totally redundant :)

---
