# #192 Try to detect VS2017 using vswhere [Merged]

> Username: refack  
> Created at: 2017-04-21 00:31:17 UTC  
> Updated at: 2021-10-02 22:18:35 UTC  
> Merged at: 2017-06-27 11:54:51 UTC  
> Closed at: 2017-06-27 11:54:51 UTC  
> Url: https://github.com/boostorg/build/pull/192  

Simple try/fail to use `vswhere`  
A little bit more help to solve #157

---

## Comment 1

> Username: refack  
> Created_at: 2017-04-21 00:31:30 UTC  
> Url: https://github.com/boostorg/build/pull/192#issuecomment-295986240  

/cc @KindDragon

---

## Review 2 [Commented]

> Username: KindDragon  
> Created_at: 2017-04-21 07:54:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/192#pullrequestreview-33958608  

📁 src/engine/guess_toolset.bat

```diff
  41 |+ if not exist %VSWHERE% set VSWHERE="%ProgramFiles%\Microsoft Visual Studio\Installer\vswhere.exe"
  42 |+ if not exist %VSWHERE% goto :skip_vswhere
  43 |+ for /f "usebackq tokens=*" %%i in (`%VSWHERE% -latest -products %VSWHERE_IDS% %VSWHERE_REQ% %VSWHERE_PRP%`) do (
```

> Username: KindDragon  
> Created_at: 2017-04-21 07:54:48 UTC  
> Updated_at: 2017-05-06 16:10:54 UTC  
> Url: https://github.com/boostorg/build/pull/192#discussion_r112629354  

Thanks for ping. I think better use `-products *` instead of IDS and add version here `-version "[15.0,16.0)"`  
  
`%VSWHERE% -latest -version "[15.0,16.0)" -products * %VSWHERE_REQ% %VSWHERE_PRP%`

> Username: refack  
> Created_at: 2017-04-21 11:32:27 UTC  
> Updated_at: 2017-05-06 16:10:54 UTC  
> Url: https://github.com/boostorg/build/pull/192#discussion_r112668452  

ASAIK `-products *` does not cover "Buildtools" ;( (Microsoft/vswhere#8) but I'll check.  
`-latest` is supposed to be kinda similar to `-version "[15.0,16.0)"`, no?

> Username: KindDragon  
> Created_at: 2017-04-21 12:11:27 UTC  
> Updated_at: 2017-05-06 16:10:54 UTC  
> Url: https://github.com/boostorg/build/pull/192#discussion_r112673683  

> ASAIK -products * does not cover "Buildtools" ;( (Microsoft/vswhere#8) but I'll check.  
  
Without `-products` does not cover "Buildtools", but `-products *` cover all products https://github.com/Microsoft/vswhere/issues/23  
  
> -latest is supposed to be kinda similar to -version "[15.0,16.0)", no?  
  
"-latest" will find even theoretical "Visual Studio 2018/2019" (> 16.0). I think we should limit to VS2017 only

> Username: refack  
> Created_at: 2017-04-21 14:05:24 UTC  
> Updated_at: 2017-05-06 16:10:54 UTC  
> Url: https://github.com/boostorg/build/pull/192#discussion_r112694559  

As future proofing, If i find a have to better detect and set `"BOOST_JAM_TOOLSET=vc141"` I'd rather remove the version limit... So the code will continue to work without changes.  
Sound reasonable?

> Username: KindDragon  
> Created_at: 2017-04-21 17:29:34 UTC  
> Updated_at: 2017-05-06 16:10:54 UTC  
> Url: https://github.com/boostorg/build/pull/192#discussion_r112738764  

I did not fully understand. If you remove version limit and theoretical VS2019 will be with toolset "vc150", then we set "BOOST_JAM_TOOLSET=vc141" when bjam will use new toolset from VS2019

> Username: refack  
> Created_at: 2017-04-21 18:30:06 UTC  
> Updated_at: 2017-05-06 16:10:54 UTC  
> Url: https://github.com/boostorg/build/pull/192#discussion_r112750837  

If for vs2019/vc150 I can detect the `vc150` and set `BOOST_JAM_TOOLSET=vc150` bjam will fail ("unknown toolset vc150")  
But this code will not need to change

> Username: KindDragon  
> Created_at: 2017-04-21 18:40:07 UTC  
> Updated_at: 2017-05-06 16:10:54 UTC  
> Url: https://github.com/boostorg/build/pull/192#discussion_r112752778  

If you call vswhere without `-version "[15.0,16.0)"` it will set `BOOST_JAM_TOOLSET=vc141`, `VS150COMNTOOLS` (`BOOST_JAM_TOOLSET_ROOT`) will be set to VS2019 and bjam continue with wrong VS


---

## Review 3 [Commented]

> Username: KindDragon  
> Created_at: 2017-04-21 18:48:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/192#pullrequestreview-34097948  

📁 src/engine/guess_toolset.bat

```diff
  44 |+     set "VS150COMNTOOLS=%%i"
  45 |+     set "BOOST_JAM_TOOLSET=vc141"
  46 |+     set "BOOST_JAM_TOOLSET_ROOT=%%i\VC\"
```

> Username: KindDragon  
> Created_at: 2017-04-21 18:48:00 UTC  
> Updated_at: 2017-05-06 16:10:54 UTC  
> Url: https://github.com/boostorg/build/pull/192#discussion_r112754282  

Please also add `BOOST_JAM_TOOLSET_ROOT` detection to config_toolset.bat  
https://github.com/boostorg/build/blob/d2ff572c2399bc952f6a15a9fc1a01f7a82a4730/src/engine/config_toolset.bat#L168-L171  
  
It will be used if user call `build.bat vc141`

> Username: refack  
> Created_at: 2017-04-21 18:49:16 UTC  
> Updated_at: 2017-05-06 16:10:54 UTC  
> Url: https://github.com/boostorg/build/pull/192#discussion_r112754509  

Oh yeah right...


---

## Comment 4

> Username: refack  
> Created_at: 2017-04-21 20:36:14 UTC  
> Url: https://github.com/boostorg/build/pull/192#issuecomment-296299652  

@KindDragon addressed  
@grafikrobot I think it's complete, let's see what the CI says

---

## Comment 5

> Username: KindDragon  
> Created_at: 2017-04-21 20:56:40 UTC  
> Url: https://github.com/boostorg/build/pull/192#issuecomment-296303708  

@refack AppVeyour show message 2 times:  "The system cannot find the batch label specified - skip_vswhere"

---

## Comment 6

> Username: refack  
> Created_at: 2017-04-21 21:00:59 UTC  
> Url: https://github.com/boostorg/build/pull/192#issuecomment-296304636  

> @refack AppVeyour show message 2 times: "The system cannot find the batch label specified - skip_vswhere"  
  
Although I see it's not fatal, I fixed it.

---

## Comment 7

> Username: KindDragon  
> Created_at: 2017-05-06 15:09:48 UTC  
> Url: https://github.com/boostorg/build/pull/192#issuecomment-299645955  

Can you rebase on top of latest changes to fix AppVeyor?

---

## Comment 8

> Username: refack  
> Created_at: 2017-05-06 16:11:07 UTC  
> Url: https://github.com/boostorg/build/pull/192#issuecomment-299649707  

@KindDragon done.

---

## Comment 9

> Username: KindDragon  
> Created_at: 2017-06-07 19:41:16 UTC  
> Updated_at: 2017-06-07 19:41:37 UTC  
> Url: https://github.com/boostorg/build/pull/192#issuecomment-306903095  

Hi @grafikrobot. Do you have any news about this PR?

---

## Comment 10

> Username: refack  
> Created_at: 2017-06-27 13:44:38 UTC  
> Url: https://github.com/boostorg/build/pull/192#issuecomment-311362503  

:tada:  
If any further help needed, ping me, I'm fairly responsive...

---

## Comment 11

> Username: grafikrobot  
> Created_at: 2017-06-27 14:01:42 UTC  
> Url: https://github.com/boostorg/build/pull/192#issuecomment-311367584  

Sorry it took so long had to read it carefully and try it out and I had gobs of other things on my plate.. But even with being careful it still messed up the builds. Looks like for anything non-141 it fails to do the general bootstrap.bat procedure to build b2.exe. For example https://ci.appveyor.com/project/boostorg/boost/build/1.0.3013/job/0q1y55yeq0xauy7f#L24

---

## Comment 12

> Username: refack  
> Created_at: 2017-06-27 14:03:19 UTC  
> Url: https://github.com/boostorg/build/pull/192#issuecomment-311368044  

I'm looking into it right now.

---
