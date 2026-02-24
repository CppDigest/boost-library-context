# #115 Further fix for dll export under windows [Closed]

> Username: SPKorhonen  
> Created at: 2017-02-17 13:44:34 UTC  
> Updated at: 2018-04-09 01:12:16 UTC  
> Closed at: 2018-04-09 01:12:16 UTC  
> Url: https://github.com/boostorg/python/pull/115  

There was still some issues using boost::python::numpy under windows as a shared library. At least under MSVC using builtin dtypes is fragile and creates semi-random segfaults.   
  
This pull request fixes the remaining export issues and fixes the segfault in dtype

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2017-03-03 23:01:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/115#pullrequestreview-22504086  

Can you please respond to the comment I added two weeks ago ? I'd really like to merge this for the upcoming release, but I need to understand that change. The added `BOOST_NUMPY_DECL` decorators look good. It's the rest that I worry about.  
  
Thanks !

📁 include/boost/python/numpy/dtype.hpp

```diff
  74 | 
  75 | namespace detail
  76 | {
```

> Username: stefanseefeld  
> Created_at: 2017-02-17 14:18:57 UTC  
> Updated_at: 2017-03-03 23:01:43 UTC  
> Url: https://github.com/boostorg/python/pull/115#discussion_r101764688  

I'm trying to understand the changes you applied to this file. It looks like anything below this point as just non-semantic / formatting change. Is that true ? I.e. the only change to this file is the addition of BOOST_NUMPY_DECL to the two declarations above.  
If that's true, I'd prefer if you revert the change below, so it's clear what this patch entails. Otherwise please explain the nature of the change. Thanks !

> Username: SPKorhonen  
> Created_at: 2017-03-06 15:49:36 UTC  
> Updated_at: 2017-03-06 15:49:37 UTC  
> Url: https://github.com/boostorg/python/pull/115#discussion_r104445679  

Sorry for the delay. I seem to have missed the notification about your comment.  
  
Anyways, the most pertinent change is the sprinkling of several "BOOST_NUMPY_DECL" statements to force the generation of still missing symbols and objects.  
  
The other changes were necessitated by compilation problems. Under MSVC there were several chicken-or-the-egg -problems with the caused need to apply BOOST_NUMPY_DECL which was problematic due to the forward declarations and such. Also the whole dtype engine seemed to be rather fragile and caused semi-random segfaults depending on the builds.   
  
After a moderate diagnosis effort I noticed that if you re-arrange the code and especially the declarations in certain logical order the compiler is happy and you can simplify the code and get rid of the segfaults. So yes, apart from BOOST_NUMPY_DECL, the patch is mostly re-arranging the code, but it is done to keep compiler happy.  
  
I've been running this code under several applications and extreme load. As far as I can tell the DLLs produced appear to be rock solid.


---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2017-03-06 19:20:58 UTC  
> Url: https://github.com/boostorg/python/pull/115#issuecomment-284502621  

Could you separate this PR into one containing thi missing declspec annotations, and another with the remainder of the original patch ? Thanks.

---

## Comment 3

> Username: SPKorhonen  
> Created_at: 2017-03-07 09:57:40 UTC  
> Url: https://github.com/boostorg/python/pull/115#issuecomment-284675206  

I can.   
  
For the declspec annotations patch, should I leave the BOOST_NUMPY_DECL out where I can't add them without breaking the build on MSVC thus leaving the dtype in inconsistent state or should I add them regardless of the effect on MSVC build?

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2018-04-09 01:12:16 UTC  
> Url: https://github.com/boostorg/python/pull/115#issuecomment-379599931  

I believe all the issues your PR attempted to fix are now addressed. If not, please update your PR and resubmit.

---
