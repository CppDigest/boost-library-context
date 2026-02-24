# #773 Use system-dependent CL<->GL sharing extension name instead of hardcoded string [Merged]

> Username: krwc  
> Created at: 2018-04-11 21:13:28 UTC  
> Updated at: 2018-04-12 12:24:45 UTC  
> Merged at: 2018-04-12 12:24:39 UTC  
> Closed at: 2018-04-12 12:24:40 UTC  
> Url: https://github.com/boostorg/compute/pull/773  

It looks as though the author's intention was to use different extension name for different platforms.   
  
For some reason, however, commit 114e444a7ea88055460f8dd76c6cc76032df6b18 introduced a change that ignores the platform-specific `cl_gl_sharing_extension` variable and uses a string instead, whose value doesn't seem right (and it doesn't work on my system).

---

## Comment 1

> Username: coveralls  
> Created_at: 2018-04-11 22:28:00 UTC  
> Url: https://github.com/boostorg/compute/pull/773#issuecomment-380615801  

[![Coverage Status](https://coveralls.io/builds/16471367/badge)](https://coveralls.io/builds/16471367)  
  
Coverage remained the same at 82.121% when pulling **330a6dcfdb4300e744263ffe03557fffcac97346 on sznaider:fix-cl-gl-extension-name** into **1bf4b53f6051fb8f20823a0c780cfcafeb4175c0 on boostorg:develop**.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2018-04-12 12:24:44 UTC  
> Url: https://github.com/boostorg/compute/pull/773#issuecomment-380786661  

Thanks!

---
