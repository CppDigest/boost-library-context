# #6 Honor --bindir and --libdir options if present when building wave. [Merged]

> Username: eldiener  
> Created at: 2016-03-10 06:39:54 UTC  
> Updated at: 2016-03-10 12:41:22 UTC  
> Merged at: 2016-03-10 12:41:22 UTC  
> Closed at: 2016-03-10 12:41:22 UTC  
> Url: https://github.com/boostorg/wave/pull/6  

You should be able to tell the wave tool to build its libraries and/or executable outside of the Boost tree using the --libdir and/or --bindir command line options. The current build jamfile hardcodes the final lib and exe directory within the Boost tree. This fix allows the end-user to change that if he so desires.

---

## Comment 1

> Username: hkaiser  
> Created_at: 2016-03-10 12:41:19 UTC  
> Url: https://github.com/boostorg/wave/pull/6#issuecomment-194824266  

Thanks! LGTM!

---
