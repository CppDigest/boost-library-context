# #13 - Architecture Info [Closed]

> Username: top-master  
> Created at: 2018-02-24 10:13:17 UTC  
> Updated at: 2018-02-24 10:37:33 UTC  
> Closed at: 2018-02-24 10:31:15 UTC  
> Url: https://github.com/boostorg/atomic/issues/13  

at last found this after search in many library's:  
1: after download  
2: placed it at "3rdParty/boost/*"  
3: and then included the header "atomic.hpp"  
  
all fine until now but:  
I needed to edit the headers since I did not wanted to over-fill my include path since I do just use the "atomic" from the entire "boost" library and for other things I use "Qt".  
and also I did not wanted to confuse anyone with access to my source code.  
  
any-way I think the mentioned three steps should be enough to get started could you please do this.

---

## Comment 1

> Username: top-master  
> Created at: 2018-02-24 10:24:28 UTC  
> Updated at: 2018-02-24 10:37:33 UTC  
> Url: https://github.com/boostorg/atomic/issues/13#issuecomment-368218270  

last question is "armv6" supported or Not?  
this info is important and should get it's way into README.md somehow.  
**EDIT:**  
the below was listed as supported :-)  
gcc 4.x: i386, x86_64, ppc32, ppc64, sparcv9, armv6, alpha  
Visual Studio Express 2008/Windows XP, x86, x64, ARM

---

## Comment 2

> Username: top-master  
> Created at: 2018-02-24 10:31:15 UTC  
> Updated at: 2018-02-24 10:31:53 UTC  
> Url: https://github.com/boostorg/atomic/issues/13#issuecomment-368218639  

I think this can be closed since I have taken another look and it somehow depends on other things like <boost/config.hpp> so it is not easy to make it portable too bad.
