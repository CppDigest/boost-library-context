# #159 - Failure on msvc-12.0 [Closed]

> Username: pdimov  
> Created at: 2017-11-13 16:24:39 UTC  
> Updated at: 2017-11-24 22:23:18 UTC  
> Closed at: 2017-11-24 22:23:18 UTC  
> Url: https://github.com/boostorg/fusion/issues/159  

```  
#include <boost/fusion/container/vector.hpp>  
#include <boost/type.hpp>  
  
int main()  
{  
	boost::fusion::vector<int, float> v1, v2;  
	v1 = v2;  
}  
```  
  
```  
c:\projects\testbed2013\testbed.cpp(7): error C2036: 'boost::type' : unknown size  
```  
  
This comes up in the Spirit tests, f.ex. here:  
  
https://ci.appveyor.com/project/boostorg/boost/build/1.0.4754-develop/job/yy8ymq7qvy73ce3c

---

## Comment 1

> Username: Flast  
> Created at: 2017-11-14 13:36:54 UTC  
> Url: https://github.com/boostorg/fusion/issues/159#issuecomment-344260805  

It might be caused by #154, I'll investigate.

---

## Comment 2

> Username: Kojoley  
> Created at: 2017-11-14 14:08:01 UTC  
> Url: https://github.com/boostorg/fusion/issues/159#issuecomment-344269613  

It is not #154 if it is not on the master. Look for Spirits `qi_debug-p3`, `qi_pass_container2-p3`, `qi_pass_container2-p3` fails on MSVC 10, 11, 12.

---

## Comment 3

> Username: Flast  
> Created at: 2017-11-14 14:50:43 UTC  
> Url: https://github.com/boostorg/fusion/issues/159#issuecomment-344282343  

#154 is already merged into master. Isn't spirit master still old?

---

## Comment 4

> Username: Kojoley  
> Created at: 2017-11-14 14:58:05 UTC  
> Url: https://github.com/boostorg/fusion/issues/159#issuecomment-344284779  

> #154 is already merged into master.  
  
I see... GitHub shows only `develop` in branch list for the commit, I fooled by it the second time in the last weeks.  
  
> Isn't spirit master still old?  
  
Yes, it is.
