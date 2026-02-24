# #78 - VC++ 2017 compiler warning at wait_group.hpp(55) [Closed]

> Username: OneCodeCZ  
> Created at: 2019-03-30 12:11:27 UTC  
> Updated at: 2019-04-04 10:18:52 UTC  
> Closed at: 2019-04-04 10:18:52 UTC  
> Url: https://github.com/boostorg/process/issues/78  

The VC++2017 x86 compiler gives me this warning.  
```  
\boost\process\detail\windows\wait_group.hpp(55): warning C4244: '-=': conversion from '_Rep' to 'int', possible loss of data  
```  
Please fix it, I like to be without warnings :)
