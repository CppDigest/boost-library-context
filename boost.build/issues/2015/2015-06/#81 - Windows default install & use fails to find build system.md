# #81 - Windows default install & use fails to find build system. [Closed]

> Username: grafikrobot  
> Created at: 2015-06-01 21:02:48 UTC  
> Updated at: 2023-04-03 02:53:17 UTC  
> Closed at: 2023-04-03 02:53:16 UTC  
> Url: https://github.com/boostorg/build/issues/81  

When using BB standalone and following the default install procedures one gets errors because the default install location for BB isn't searched for the build system files. For example:  
  
``` batch  
git clone https://github.com/boostorg/build.git  
cd build  
bootstrap.bat  
b2.exe install  
cd /my-project/test  
b2.exe  
```  
  
Results in:  
  
```  
Unable to load Boost.Build: could not find "boost-build.jam"  
---------------------------------------------------------------  
BOOST_ROOT must be set, either in the environment, or   
on the command-line with -sBOOST_ROOT=..., to the root  
of the boost installation.  
  
Attempted search from C:\projects\predef\test up to the root  
at C:/boost-build-engine/share/boost-build  
  
Please consult the documentation at 'http://www.boost.org'.  
```  
  
As compared to the equivalent steps on Unix that work without the error.

---

## Comment 1

> Username: vprus  
> Created at: 2015-06-03 19:02:02 UTC  
> Url: https://github.com/boostorg/build/issues/81#issuecomment-108578015  

Rene,  
  
are you about to fix this, or want me to?

---

## Comment 2

> Username: grafikrobot  
> Created at: 2015-06-03 19:05:00 UTC  
> Url: https://github.com/boostorg/build/issues/81#issuecomment-108580120  

I'm not about to fix it.. Was just adding a future TODO for anyone who had  
the time to fix it.

---

## Comment 3

> Username: vprus  
> Created at: 2015-06-03 19:30:44 UTC  
> Url: https://github.com/boostorg/build/issues/81#issuecomment-108589235  

I've checked in a small fix in f0e3145. With that, if I do "b2 install" and then run "C:\boost-build\bin\b2", it finds the rest of the build system just fine. I am not entirely sure whether searching that location by default even if b2 is invoked from other places is good idea or not - on Ubuntu we search /usr/share/boost-build per platform requirements.
