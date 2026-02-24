# #11 Fully qualify error_info_base for Visual C++ 7.1 [Merged]

> Username: danieljames  
> Created at: 2018-04-07 14:02:49 UTC  
> Updated at: 2019-03-03 06:14:34 UTC  
> Merged at: 2019-03-03 06:14:28 UTC  
> Closed at: 2019-03-03 06:14:28 UTC  
> Url: https://github.com/boostorg/exception/pull/11  

Test are failing on Visual C++ 7.1, I think because it's not importing  
error_info_base into the class' namespace, so hopefully this will fix it. I  
don't have access to the compiler, so I'm not sure.

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-11-12 22:05:15 UTC  
> Url: https://github.com/boostorg/exception/pull/11#issuecomment-438044578  

Visual Studio 2005, 2008 are beyond EOL:  
https://support.microsoft.com/en-us/lifecycle/search?alpha=Microsoft%20Visual%20Studio%202008  
  
Do we need it?

---

## Comment 2

> Username: zajo  
> Created_at: 2019-03-03 06:14:33 UTC  
> Url: https://github.com/boostorg/exception/pull/11#issuecomment-468992851  

Thank you!

---
