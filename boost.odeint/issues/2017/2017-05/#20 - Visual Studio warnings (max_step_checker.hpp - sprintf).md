# #20 - Visual Studio warnings (max_step_checker.hpp / sprintf) [Closed]

> Username: slayoo  
> Created at: 2017-05-22 09:58:21 UTC  
> Updated at: 2023-12-18 09:35:30 UTC  
> Closed at: 2023-12-18 09:35:30 UTC  
> Url: https://github.com/boostorg/odeint/issues/20  

```  
  C:\devel\boost_1_64_0\boost/numeric/odeint/integrate/max_step_checker.hpp(72): warning C4996: 'sprintf': This function or variable may be unsafe. Consider using sprintf_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [C:\devel\vinecopulib\build\vinecopulib.vcxproj]  
  C:\devel\boost_1_64_0\boost/numeric/odeint/integrate/max_step_checker.hpp(104): warning C4996: 'sprintf': This function or variable may be unsafe. Consider using sprintf_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details. [C:\devel\vinecopulib\build\vinecopulib.vcxproj]  
```  
  
HTH,  
Sylwester

---

## Comment 1

> Username: slayoo  
> Created at: 2019-04-29 19:47:20 UTC  
> Url: https://github.com/boostorg/odeint/issues/20#issuecomment-487717696  

Hello,  
  
I've not used VS for over a year and dont' have access to it right now,   
but perhaps we could set up an Appveyor build to have a Windows CI and   
expose such warnings?  
  
Sylwester  
  
On 15/04/2019 14:34, MagicTiger wrote:  
> Do you still have the issue with the latest release version? If so, can   
> you detail the steps for replication?  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/odeint/issues/20#issuecomment-483215681>,   
> or mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AAFeVeYvxV7sfNIhSiF5JmnXCwtipUVPks5vhGO4gaJpZM4NiInT>.  
>

---

## Comment 2

> Username: slayoo  
> Created at: 2019-04-29 19:48:41 UTC  
> Url: https://github.com/boostorg/odeint/issues/20#issuecomment-487718148  

https://github.com/boostorg/odeint/issues/37
