# #59 apply consistent include guards to headers [Closed]

> Username: rarevans  
> Created at: 2018-11-19 10:54:06 UTC  
> Updated at: 2019-01-17 01:19:53 UTC  
> Closed at: 2019-01-17 01:19:53 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/59  

fix incorrect file name in guard define  
consistently include hpp header extension in guard define

---

## Comment 1

> Username: rarevans  
> Created_at: 2018-11-19 22:18:20 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/59#issuecomment-440062169  

Hi,  
I have assumed it is okay to use the BOOST_NUMERIC prefix for guards even though we are in BOOST_SAFE_NUMERICS. I wasn't sure if there was a plan to have this library eventually live in the numeric folder.  
I checked the build messages. The build has the same failure messages as the previous auto builds. I am not sure if removing the -j 3 argument to the b2 build would allow better comparison of the build messages. The current b2 output with multiple concurrent builds has interleaved messages.

---
