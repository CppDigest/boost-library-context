# #26 - Potential invalid __CONO_VERSION__ [Closed]

> Username: ccharly  
> Created at: 2015-07-08 08:00:55 UTC  
> Updated at: 2015-07-08 11:27:30 UTC  
> Closed at: 2015-07-08 11:27:30 UTC  
> Url: https://github.com/boostorg/predef/issues/26  

Hi, I was taking a look to the actual structure of this project and I've spotted that seemed to be invalid.  
  
[Here](https://github.com/boostorg/predef/blob/0f113b0871832aafdffed45a5def8f473161cdab/include/boost/predef/compiler/comeau.h#L32).  
  
It seems that `__CONO_VERSION__` should be: `__COMO_VERSION__` (according to what I've found on the internet as well as the logic of the code following that line).  
  
Am I wrong?

---

## Comment 1

> Username: grafikrobot  
> Created at: 2015-07-08 11:27:30 UTC  
> Url: https://github.com/boostorg/predef/issues/26#issuecomment-119545668  

You are correct.. Thanks for spotting that.
