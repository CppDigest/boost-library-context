# #1004 Updated Deprecated submodule URL in .gitmodules [Open]

> Username: hemanggs  
> Created at: 2025-01-22 18:06:33 UTC  
> Updated at: 2025-04-05 21:53:11 UTC  
> Url: https://github.com/boostorg/boost/pull/1004  

I stumbled upon this issue while building a project that uses the boost library as submodule.  
  
Issue being faced while building is very similar to: https://github.com/bfgroup/b2/issues/323  
  
The build submodule repository seems to be shifted but the `.submodule ` has not been updated , resulting in build failing for the project i'm trying to build ( [Organic Maps](https://github.com/organicmaps/organicmaps/tree/master)) which uses the boost library as a submodule.   
  
  
The build submodule Repository https://github.com/boostorg/build  seems to have moved to https://github.com/bfgroup/b2  
Reference : https://github.com/boostorg/build/issues/614#issuecomment-850859613  
  
NOTE:  This is my first attempt at a open source PR , Kindly forgive if there is a mistake on my part .

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2025-04-05 21:53:10 UTC  
> Url: https://github.com/boostorg/boost/pull/1004#issuecomment-2781107351  

This is intentional. I realize it's not intuitive. But Boost delays which B2 version gets used for various reasons. And accomplishes that by copying the released B2 to the local git repo.

---
