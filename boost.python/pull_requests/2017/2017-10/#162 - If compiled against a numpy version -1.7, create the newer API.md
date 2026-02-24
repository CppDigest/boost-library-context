# #162 If compiled against a numpy version <1.7, create the newer API [Closed]

> Username: mborgerding  
> Created at: 2017-10-05 14:38:38 UTC  
> Updated at: 2018-04-09 01:10:31 UTC  
> Closed at: 2018-04-09 01:10:31 UTC  
> Url: https://github.com/boostorg/python/pull/162  



---

## Review 1 [Changes requested]

> Username: stefanseefeld  
> Created_at: 2017-10-05 15:12:20 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/python/pull/162#pullrequestreview-67410010  

This looks dangerous: what NumPy versions have you checked this with ? There surely is a lower limit for the NumPy version for this to work (and thus it should be clearly stated both in the inline docs as well as in the conditional)

---
