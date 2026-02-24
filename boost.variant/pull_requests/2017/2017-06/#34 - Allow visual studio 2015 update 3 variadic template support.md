# #34 Allow visual studio 2015 update 3 variadic template support [Closed]

> Username: russellmcc  
> Created at: 2017-06-29 21:27:51 UTC  
> Updated at: 2017-07-05 19:58:04 UTC  
> Closed at: 2017-07-05 14:22:30 UTC  
> Url: https://github.com/boostorg/variant/pull/34  



---

## Comment 1

> Username: apolukhin  
> Created_at: 2017-06-30 07:04:39 UTC  
> Url: https://github.com/boostorg/variant/pull/34#issuecomment-312192070  

Unfortunately it still fails the test: https://ci.appveyor.com/project/apolukhin/variant/build/1.64.27-develop  
  
The MSVC templates are still buggy.

---

## Comment 2

> Username: russellmcc  
> Created_at: 2017-07-05 14:22:30 UTC  
> Url: https://github.com/boostorg/variant/pull/34#issuecomment-313118064  

That's too bad.  Thanks for the consideration!

---

## Comment 3

> Username: apolukhin  
> Created_at: 2017-07-05 19:58:04 UTC  
> Url: https://github.com/boostorg/variant/pull/34#issuecomment-313209728  

Thanks for the pull request!  
  
You can always run the test manually:  
```  
cd boost-folder/libs/variant/test  
..\..\..\b2.exe -a  
```  
  
One day, when everything will be OK with variadic templates on MSVC, I'd happy to merge the pull request.

---
