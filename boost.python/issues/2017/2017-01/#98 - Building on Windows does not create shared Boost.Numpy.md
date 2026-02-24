# #98 - Building on Windows does not create shared Boost.Numpy [Closed]

> Username: dolfim  
> Created at: 2017-01-23 19:06:29 UTC  
> Updated at: 2017-02-16 06:30:28 UTC  
> Closed at: 2017-02-16 06:17:44 UTC  
> Url: https://github.com/boostorg/python/issues/98  

At conda-forge/boost-feedstock#32 we are trying to finalize the Boost Python package on Conda.  
The build currently fails while copying ```boost_numpy-vc90-mt-1_63.lib```, because the file does not exist.  
  
The full log is at: boost_numpy-vc90-mt-1_63.lib  
The build command is:  
```batch  
.\b2 install ^  
    --build-dir=buildboost ^  
    --prefix=%LIBRARY_PREFIX% ^  
    toolset=msvc-%VS_MAJOR%.0 ^  
    address-model=%ARCH% ^  
    variant=release ^  
    threading=multi ^  
    link=static,shared ^  
    --with-python ^  
    -j%CPU_COUNT%  
```  
  
Is there something wrong in the build process, or did we find an issue in the Boost building process?

---

## Comment 1

> Username: jakirkham  
> Created at: 2017-01-23 19:12:48 UTC  
> Url: https://github.com/boostorg/python/issues/98#issuecomment-274586846  

I think the link to the log got lost. Linked below.  
  
Note: This seems to happen with all Boost.Python builds on Windows, but not on Unix.  
  
ref: https://ci.appveyor.com/project/conda-forge/boost-feedstock/build/1.0.97

---

## Comment 2

> Username: jakirkham  
> Created at: 2017-02-09 15:56:18 UTC  
> Url: https://github.com/boostorg/python/issues/98#issuecomment-278683942  

Any help on this would be greatly appreciated. We're kind of at a loss ATM.

---

## Comment 3

> Username: jhunold  
> Created at: 2017-02-09 19:31:26 UTC  
> Updated at: 2017-02-09 19:31:59 UTC  
> Url: https://github.com/boostorg/python/issues/98#issuecomment-278747242  

It is the other way round. Building the shared libraries fails as the missing `lib`-prefix indicates the shared library. This is no wonder as the necessary dllexport/-import magic is completely missing from Boost.Numpy. The msvc linker thus refuses to create the empty `boost_numpy-vc90-mt-1_63.lib` stub which is needed by the `install`routine.

---

## Comment 4

> Username: jakirkham  
> Created at: 2017-02-09 19:38:28 UTC  
> Url: https://github.com/boostorg/python/issues/98#issuecomment-278749112  

Yeah, sorry, we just realized we had it backwards earlier.  
  
So what sort of change would we be looking at?

---

## Comment 5

> Username: XWarin  
> Created at: 2017-02-14 10:20:38 UTC  
> Url: https://github.com/boostorg/python/issues/98#issuecomment-279666757  

Hi !  
  
I stumbled on that issue yesterday  trying to get rid of the old (independent) boost numpy.  
Is this there something scheduled to correct this dynamic link problem ?  
Best regards

---

## Comment 6

> Username: SPKorhonen  
> Created at: 2017-02-15 14:20:05 UTC  
> Url: https://github.com/boostorg/python/issues/98#issuecomment-280022588  

I also stumbled onto this problem when trying to update boost to 1.63 to get rid of separate boost::numpy dependency.   
  
The problem is that, at least on windows, numpy generates empty shared libraries (the dll is generated but exports no symbols). This is due to the windows dll default settings. Everything that is not explicitly exported is private. As @jhunold stated the numpy is missing dllexport/-import magic words. I think that liberal sprinkling of BOOST_PYTHON_DECL before all symbols that are to be exported would help.

---

## Comment 7

> Username: SPKorhonen  
> Created at: 2017-02-15 15:30:11 UTC  
> Url: https://github.com/boostorg/python/issues/98#issuecomment-280042328  

I could make an effort on fixing this issue if nobody else has free time to devote to resolving this...

---

## Comment 8

> Username: XWarin  
> Created at: 2017-02-15 15:38:47 UTC  
> Url: https://github.com/boostorg/python/issues/98#issuecomment-280044902  

Hi  
  
It would be a good idea. I don't use much windows and i only port my work from linux.  
Right now when on windows i still recompile the old  boost numpy.  
Best regards

---

## Comment 9

> Username: jakirkham  
> Created at: 2017-02-15 15:52:29 UTC  
> Url: https://github.com/boostorg/python/issues/98#issuecomment-280049266  

Seconding @XWarin. Some of us have limited knowledge of building on Windows and/or time to devote to this. As it sounds like you have both knowledge and time, @SPKorhonen, I'd say go for it. We would be very appreciative. Can help test patches if you would like.

---

## Comment 10

> Username: stefanseefeld  
> Created at: 2017-02-15 15:54:58 UTC  
> Updated at: 2017-02-15 15:55:14 UTC  
> Url: https://github.com/boostorg/python/issues/98#issuecomment-280049989  

I have just posted a PR at https://github.com/boostorg/python/pull/108 in an attempt to fix this. I'd appreciate if someone could confirm, before I merge.

---

## Comment 11

> Username: jakirkham  
> Created at: 2017-02-15 17:05:57 UTC  
> Url: https://github.com/boostorg/python/issues/98#issuecomment-280071976  

So I guess this should be fixed by PR ( https://github.com/boostorg/python/pull/110 ). Will try to test this and let you know how it goes.

---

## Comment 12

> Username: dolfim  
> Created at: 2017-02-15 21:11:09 UTC  
> Url: https://github.com/boostorg/python/issues/98#issuecomment-280139899  

We added the patch to the conda-forge package. Now it will try building here: https://ci.appveyor.com/project/conda-forge/boost-feedstock/build/1.0.109  
  
Thanks a lot for the patch.

---

## Comment 13

> Username: dolfim  
> Created at: 2017-02-16 06:17:44 UTC  
> Url: https://github.com/boostorg/python/issues/98#issuecomment-280241814  

It seems to work correctly!

---

## Comment 14

> Username: XWarin  
> Created at: 2017-02-16 06:30:27 UTC  
> Url: https://github.com/boostorg/python/issues/98#issuecomment-280243732  

Thank you very much !  
  
2017-02-16 7:17 GMT+01:00 Michele Dolfi <notifications@github.com>:  
  
> Closed #98 <https://github.com/boostorg/python/issues/98>.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/python/issues/98#event-964424332>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ALThAaWS9w0hWP6luMqW0Ktre1WMlFlaks5rc-oJgaJpZM4Lra8B>  
> .  
>
