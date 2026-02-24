# #2438 - fatal error: boost/config.hpp: No such file or directory [Closed]

> Username: BernardoOlisan  
> Created at: 2022-05-25 12:55:11 UTC  
> Updated at: 2022-05-26 03:26:05 UTC  
> Closed at: 2022-05-26 03:26:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2438  

So I install boost beast lib but the problem is that it always throws this errors.  
![image](https://user-images.githubusercontent.com/46250121/170266592-73441aee-ad83-40e7-b950-88bc9f7dff68.png)  
I have only this   
![image](https://user-images.githubusercontent.com/46250121/170266640-8407cb12-9f98-43bf-8d19-dcfde9b565a3.png)  
where boost is the only thing I used   
![image](https://user-images.githubusercontent.com/46250121/170266778-819052d9-07db-4392-868b-c8f998a175bd.png)  
any idea?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-05-25 14:49:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2438#issuecomment-1137371066  

How do you "install boost beast lib" ?

---

## Comment 2

> Username: BernardoOlisan  
> Created at: 2022-05-25 14:59:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2438#issuecomment-1137392837  

> How do you "install boost beast lib" ?  
  
I installed only boost beast and ASIO

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-05-25 15:00:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2438#issuecomment-1137393763  

> I installed only boost beast and ASIO  
  
You need to "install" Boost.

---

## Comment 4

> Username: grafikrobot  
> Created at: 2022-05-25 15:28:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2438#issuecomment-1137439526  

Have you considered using a package manager?

---

## Comment 5

> Username: BernardoOlisan  
> Created at: 2022-05-25 15:34:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2438#issuecomment-1137446978  

> > I installed only boost beast and ASIO  
>   
>   
>   
> You need to "install" Boost.  
  
The whole boost?! Is like 1.5gb

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-05-25 15:42:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2438#issuecomment-1137455519  

> The whole boost?! Is like 1.5gb  
  
So?

---

## Comment 7

> Username: BernardoOlisan  
> Created at: 2022-05-25 15:48:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2438#issuecomment-1137461466  

> > The whole boost?! Is like 1.5gb  
>   
> So?  
  
is bc, I only want to use the beast and asio

---

## Comment 8

> Username: glenfe  
> Created at: 2022-05-25 15:53:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2438#issuecomment-1137467490  

@BernardoOlisan beast depends on: asio, assert, bind, config, container, container_hash, core, endian, intrusive, logic, mp11, optional, smart_ptr, static_assert, system, throw_exception, type_index, type_traits, utility, and winapi.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-05-25 16:00:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2438#issuecomment-1137474248  

>  I only want to use the beast and asio  
  
It is easier to just install Boost then to try to pick out individual libraries and their dependencies. Are you short on hard drive space? How much free space do you have?

---

## Comment 10

> Username: BernardoOlisan  
> Created at: 2022-05-25 16:38:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2438#issuecomment-1137527872  

> >  I only want to use the beast and asio  
>   
>   
>   
> It is easier to just install Boost then to try to pick out individual libraries and their dependencies. Are you short on hard drive space? How much free space do you have?  
>   
>   
No, the thing is that, what if I want to use this lib in a project... the project will be 1.5gb :/

---

## Comment 11

> Username: pdimov  
> Created at: 2022-05-25 16:48:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2438#issuecomment-1137543126  

Looks like you only need header-only libraries, so the `boost/` directory should be enough; it's 142MB for me, which is a lot, but not 1.5GB.

---

## Comment 12

> Username: alandefreitas  
> Created at: 2022-05-25 18:46:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2438#issuecomment-1137709919  

> No, the thing is that, what if I want to use this lib in a project... the project will be 1.5gb :/  
  
No, it won't. Unless you are working on the Boost libraries, Boost should be a dependency in your project. Not part of it.  
  
Also, there are no circular dependencies between Beast and other Boost (sub-)libraries. So assuming you're talking about the size of your project source code:  
  
- If you add `find_package(Boost)` to your `CMakelists.txt`, that's **19 bytes** of information. This will look for the system boost installation.  
- Now if you want to **bundle** (not simply use) the _whole_ Boost project in your own project (which you shouldn't usually do), then you have the **142MB** @pdimov just mentioned.   
- If you want to go one step further (which again you shouldn't usually do), you could only **bundle** the subset of boost (sub-)libraries required by Boost.Beast (using [this script](https://github.com/boostorg/boostdep/blob/develop/depinst/depinst.py), for instance) and remove anything but the header files. I just did that and that's **60,9MB**.  
- Even if you want a combination of `find_package(Boost)` in your `CMakelists.txt` when the user has Boost installed and `FetchContent` when the user doesn't have Boost installed, you could even use `FetchContent` only for the [beast dependencies](https://pdimov.github.io/boostdep-report/master/beast.html) in a loop.  
  
That's assuming you're talking about the size of your project source code. If we assume you're talking about the size of your binaries, then there's nothing to worry about: just don't include what you don't use.
