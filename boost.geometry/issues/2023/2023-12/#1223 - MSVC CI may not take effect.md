# #1223 - MSVC CI may not take effect [Closed]

> Username: FantasqueX  
> Created at: 2023-12-10 13:43:24 UTC  
> Updated at: 2025-05-20 12:49:37 UTC  
> Closed at: 2025-05-20 12:49:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/1223  

As you can see in [the latest CI result](https://github.com/boostorg/geometry/actions/runs/7101383204/job/19329377831), `warning: Did not find command for MSVC toolset. If you have Visual Studio 2017 installed you will need to specify the full path to the command, set VS150COMNTOOLS for your installation, or build from the 'Visual Studio Command Prompt for VS 2017'.` and no test is executed.  
  
The problem is that there is no msvc-15 or msvc-16. MSVC supports c++14 from vs2015 a.k.a. msvc-14.0. b2_toolset should be  
```  
b2_toolset: [  
  msvc-14.0,  
  msvc-14.1,  
  msvc-14.2,  
  msvc-14.3,  
]  
```

---

## Comment 1

> Username: vissarion  
> Created at: 2023-12-13 10:09:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/1223#issuecomment-1853623770  

Thanks for opening this. It seems that msvc-14.0 -- msvc-14.2 return the same error (see https://github.com/boostorg/geometry/pull/1224).  Probably there is a need for extra setting to make them work on CI.

---

## Comment 2

> Username: FantasqueX  
> Created at: 2023-12-13 13:00:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1223#issuecomment-1853874674  

On my machine (Windows 11 with vs2019 installed), `./b2 toolset=msvc-14.2` works well.  
According to [Github action documentation](https://github.com/actions/runner-images/blob/main/images/windows/Windows2022-Readme.md), both vc142 and vc143 of vs2022 are installed. I don't know why b2 cannot find vc142. Maybe some more investigation needs to be done.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2024-09-30 18:18:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/1223#issuecomment-2383872374  

@vissarion , this looks outdated, can we close it?

---

## Comment 4

> Username: vissarion  
> Created at: 2024-10-02 10:06:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1223#issuecomment-2388228213  

@barendgehrels the warning still exists in latest CI (the minimal / msvc-15 and minimal / msvc-16 jobs are green but there is a hidden warning and actually tests are not run at all).

---

## Comment 5

> Username: barendgehrels  
> Created at: 2024-10-02 15:20:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/1223#issuecomment-2388955879  

OK thanks for checking. Then we can keep it open.  
Personally, I don't use `b2` anymore.
