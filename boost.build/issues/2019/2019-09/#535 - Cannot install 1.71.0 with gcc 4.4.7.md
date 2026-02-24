# #535 - Cannot install 1.71.0 with gcc 4.4.7 [Closed]

> Username: jsg72  
> Created at: 2019-09-13 18:30:18 UTC  
> Updated at: 2020-12-22 20:49:15 UTC  
> Closed at: 2020-12-22 20:49:15 UTC  
> Url: https://github.com/boostorg/build/issues/535  

The docs for 1.71.0 say it was tested with gcc 4.4.7, but I cannot install it.  bootstrap.sh fails because g++ doesn't recognize the -std=c++11 switch.  I tried hacking tools/build/src/engine/build.sh to use -std=c++0x instead of c++11, but then I got a build failure:  
  
```  
In file included from jam.cpp:137:  
sysinfo.h:41: error: ISO C++ forbids initialization of member 'cpu_core_count_'  
sysinfo.h:41: error: making 'cpu_core_count_' static  
sysinfo.h:41: error: ISO C++ forbids in-class initialization of non-const static member 'cpu_core_count_'  
<and more like this>  
```  
  
I see that Boost.Build is tested with gcc 4.7 and above.  If Boost supports gcc 4.4 but Boost.Build does not, how does one install Boost for 4.4?  If I build b2 from an older Boost, will it work robustly to install 1.71?

---

## Comment 1

> Username: Volvox55  
> Created at: 2020-02-19 11:35:09 UTC  
> Updated at: 2020-02-19 11:36:36 UTC  
> Url: https://github.com/boostorg/build/issues/535#issuecomment-588177311  

I have the same issue here. For a couple of reasons I'm bound to gcc 4.1.x and can't compile the boost lib 1.71.0 and above, because bootstrap.sh sets the gcc parameter -std=c++11 by default. The last working version of boost with this compiler was 1.70.0.  
  
Is there any way to fix this? As I understand the problem is not boost itself, but the BoostBuild system. Can I use BoostBuild from an older boost version to fix this?

---

## Comment 2

> Username: jsg72  
> Created at: 2020-02-19 20:31:10 UTC  
> Url: https://github.com/boostorg/build/issues/535#issuecomment-588434665  

I ultimately built b2 using an older version of boost and used it to install 1.71.0.  I don't know if that's entirely correct, but I haven't noticed any problems.  
  
There doesn't seem to be any other way to install boost without a C++11 compiler.  
  
> On Feb 19, 2020, at 6:35 AM, Volvox55 <notifications@github.com> wrote:  
>   
> I have the same issue here. For a couple of reasons I'm bound to gcc 4.1.x and can't compile the boost lib 1.71.0 and above, because bootstrap.sh sets the gcc parameter -std=c++11 by default. The last working version of boost with this compiler was 1.70.0.  
>   
> Is there any way to fix this? As I understand the problem is not boost itself, but the BoostBuid system. Can I use BoostBuild from an older boost version to fix this?  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/build/issues/535?email_source=notifications&email_token=AH3JGXFHQWVQZWI4HNPGAIDRDUKO5A5CNFSM4KPLSF3KYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOEMHN7HY#issuecomment-588177311>, or unsubscribe <https://github.com/notifications/unsubscribe-auth/AH3JGXCVKCXMJQBAINWUVHTRDUKO5ANCNFSM4KPLSF3A>.  
>

---

## Comment 3

> Username: grafikrobot  
> Created at: 2020-12-22 20:49:15 UTC  
> Url: https://github.com/boostorg/build/issues/535#issuecomment-749768948  

We are now using more C++11 features, and more to come. It's not tractable to support ongoing b2 engine development below C++11.
