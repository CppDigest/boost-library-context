# #666 - Build system can't find correct python even with help [Open]

> Username: TDCRich  
> Created at: 2020-10-28 17:35:00 UTC  
> Updated at: 2021-05-29 17:22:18 UTC  
> Url: https://github.com/boostorg/build/issues/666  

I'm trying to build Boost 1.74.0 on Amazon Linux 2. AL2 defaults to including python 2.7, but I've installed Python 3.7, and informed Boost's bootstrap program about this preferred Python. But it's failing because it's still attempting to build libraries for Python2.7, and I don't even have the development files installed for that. So, as above, it can't find pyconfig.h. But I told it to use 3.7, and gave it the include and lib paths to boot.  
  
The way Python is installed on this is that /usr/bin/python is 2.7, and /usr/bin/python3 is 3.7. Somewhere I haven't found, it's just using the "python" default and not using the "using" declarations I gave it. HELP!

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 17:21:47 UTC  
> Url: https://github.com/boostorg/build/issues/666#issuecomment-850868206  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
