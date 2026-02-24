# #33 Fixed relative path of sub project libraries to be /boostorg/* [Closed]

> Username: ENikS  
> Created at: 2014-08-24 02:58:40 UTC  
> Updated at: 2017-10-26 15:11:43 UTC  
> Closed at: 2017-10-26 15:11:43 UTC  
> Url: https://github.com/boostorg/boost/pull/33  

After forking super project 'boost' and trying to update modules git reports error: It expects child sub-projects to be located in parent directory so every single sub-project must be forked as well.  
I changed it to point to 'home' directory of the repository /boostorg/\* so sub projects could be updated from there and only forked if required.

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-26 14:21:03 UTC  
> Url: https://github.com/boostorg/boost/pull/33#issuecomment-339681983  

@danieljames should this PR be closed?  It is targeting the wrong branch, and I'm not sure if the solution is valid.

---

## Comment 2

> Username: ENikS  
> Created_at: 2017-10-26 14:25:41 UTC  
> Url: https://github.com/boostorg/boost/pull/33#issuecomment-339683508  

Well, solution might not be valid but the problem is real.   
I could resubmit to proper branch if you tell me were or you could just solve it on your own

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-10-26 15:07:19 UTC  
> Url: https://github.com/boostorg/boost/pull/33#issuecomment-339696980  

All new changes go into the `develop` branch.  
  
As for the change, this assumes that you first created a `boostorg` directory and then cloned boost into it.  I do not have this structure locally.  In my home directory I have `~/boost/libs/...` so this change would break my environment.  
  
I have been using the current structure for a while and I haven't experienced the problem you have.  Right now I only have forks for 5, maybe 6 of the total.  I do not have a fork of `boost` however.  Even if I did, the structure would be the same.

---
