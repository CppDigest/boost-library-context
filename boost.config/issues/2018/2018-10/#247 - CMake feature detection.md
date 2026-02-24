# #247 - CMake feature detection [Open]

> Username: rleigh-codelibre  
> Created at: 2018-10-16 13:38:39 UTC  
> Updated at: 2018-10-25 11:46:12 UTC  
> Url: https://github.com/boostorg/config/issues/247  

As mentioned in https://lists.boost.org/Archives/boost/2018/10/243729.php I would be happy to implement this type of functionality.  
  
Would a PR implementing the detection logic be acceptable?  Or is someone already working on this?  
  
  
Thanks,  
Roger

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-10-16 16:58:47 UTC  
> Url: https://github.com/boostorg/config/issues/247#issuecomment-430315243  

I'm quite certain that I'm not qualified to comment - anyone else?

---

## Comment 2

> Username: eldiener  
> Created at: 2018-10-22 19:43:55 UTC  
> Url: https://github.com/boostorg/config/issues/247#issuecomment-431954872  

I am not qualified to judge a CMake implementation either. I did suggest that doing this for config ( and predef also ) and then having current Boost libraries which are converted to CMake use the CMake implementations in config and predef to replace the bjam code they already have now is a much better solution than having each library do their own CMake implementation for what they will need.

---

## Comment 3

> Username: rleigh-codelibre  
> Created at: 2018-10-25 11:46:12 UTC  
> Url: https://github.com/boostorg/config/issues/247#issuecomment-433019587  

I have done some initial work on this in the linked PR (#249).  There will be more to come, this is just the initial step to make it installable and re-usable by downstream consumers.
