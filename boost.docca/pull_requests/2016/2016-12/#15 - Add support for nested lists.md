# #15 Add support for nested lists [Closed]

> Username: bachase  
> Created at: 2016-12-22 00:15:27 UTC  
> Updated at: 2019-09-15 20:19:47 UTC  
> Closed at: 2019-09-15 20:19:47 UTC  
> Url: https://github.com/boostorg/docca/pull/15  

Updateded xslt transformation to support nested unordered & ordered lists.  
  
Example output   
![image](https://cloud.githubusercontent.com/assets/14430/21407783/43314cea-c79f-11e6-8d4a-38679c30f055.png)

---

## Review 1 [Commented]

> Username: bachase  
> Created_at: 2016-12-22 00:17:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/docca/pull/15#pullrequestreview-14076381  

📁 example/Jamroot

```diff
   1 |+ #
```

> Username: bachase  
> Created_at: 2016-12-22 00:17:32 UTC  
> Updated_at: 2017-03-29 15:58:39 UTC  
> Url: https://github.com/boostorg/docca/pull/15#discussion_r93546319  

I added this so that `b2` could run on the example.  Does `docca` assume a `Jamroot` already exists for the project?  If so, I will remove.

> Username: vinniefalco  
> Created_at: 2017-03-25 20:35:06 UTC  
> Updated_at: 2017-03-29 15:58:39 UTC  
> Url: https://github.com/boostorg/docca/pull/15#discussion_r108044726  

Yeah you have to remove Jamroot, it shouldn't be there.


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-03-26 21:46:12 UTC  
> Url: https://github.com/boostorg/docca/pull/15#issuecomment-289318926  

Hell yes!!

---

## Comment 3

> Username: bachase  
> Created_at: 2017-03-28 18:04:59 UTC  
> Url: https://github.com/boostorg/docca/pull/15#issuecomment-289855012  

Amended and force pushed to drop the `Jamroot`

---
