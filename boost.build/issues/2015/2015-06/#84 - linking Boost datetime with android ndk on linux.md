# #84 - linking Boost datetime with android ndk on linux [Closed]

> Username: ericqu  
> Created at: 2015-06-22 21:53:42 UTC  
> Updated at: 2015-06-27 13:03:47 UTC  
> Closed at: 2015-06-24 13:42:46 UTC  
> Url: https://github.com/boostorg/build/issues/84  

Hi,   
  
Sorry if I am posting this in the wrong area, but I couldn't find a solution anywhere else. please point me in the right direction if needed.  
I am trying to build the date-time library on a linux (ubuntu) for Android (ideally all platforms arm etc.).  
I am expecting to pass some argument to b2 to give the appropriate include directories (and it worked fine). I wanted to do the same for the directories where the existing libraries are (like crtbegin_so.o).  
I tried to pass the -L via the linkflags (either on the commend line or in the project-config.jam but in both case I can see that the information is passed to gcc however not to ld.  
I am not sure how to find out what is wrong and how to fix it. I tried the --debug-configuration --debug-building --debug-generator but nothing obvious appears.  
Any help greatly appreciated.  
  
Eric

---

## Comment 1

> Username: KayEss  
> Created at: 2015-06-23 07:29:36 UTC  
> Url: https://github.com/boostorg/build/issues/84#issuecomment-114390053  

You can chop out the bits you need from this. It uses the Android NDK build system rather than boost build which you may find easier to integrate with other things you're building for Android.  
  
https://github.com/KayEss/fost-android-ndk

---

## Comment 2

> Username: ericqu  
> Created at: 2015-06-23 17:24:00 UTC  
> Url: https://github.com/boostorg/build/issues/84#issuecomment-114578286  

thanks KayEss, I will look into it as I see that you are using the openssl library which I also intend to use.  
The reason I was thinking of using Boost Build, is because other libraries I intended to use also used BoostBuild and I wanted to ensure they are all build with the same parameters.

---

## Comment 3

> Username: KayEss  
> Created at: 2015-06-24 10:38:06 UTC  
> Url: https://github.com/boostorg/build/issues/84#issuecomment-114819627  

Yeah, I've pulled all of the common parameters into a single file so it should be simple enough to tweak there. But the downside of course is that you'll have to build your own makefiles for any other projects you need to use.

---

## Comment 4

> Username: grafikrobot  
> Created at: 2015-06-24 13:42:46 UTC  
> Url: https://github.com/boostorg/build/issues/84#issuecomment-114871829  

You should add the extra link paths with the "library-path" feature. Any "library-path"(s) you have will get added to the linker command with the correct "-L" option.

---

## Comment 5

> Username: ericqu  
> Created at: 2015-06-27 12:57:06 UTC  
> Updated at: 2015-06-27 13:03:47 UTC  
> Url: https://github.com/boostorg/build/issues/84#issuecomment-116041023  

Thanks Graphikrobot, but I didn't manage to make it work.  
I tried to use the "library-path" feature (I assumed it needed bracket i.e. <library-path>/hom...) in the b2 command line and in the project-config.jam but it gives the same result: crtbegin_so.o not found while it is present in the directory.
