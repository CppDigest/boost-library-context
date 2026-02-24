# #40 add appveyor.yml [Merged]

> Username: rlenhardt  
> Created at: 2018-10-06 15:12:32 UTC  
> Updated at: 2018-10-17 16:03:06 UTC  
> Merged at: 2018-10-17 16:03:06 UTC  
> Closed at: 2018-10-17 16:03:06 UTC  
> Url: https://github.com/boostorg/wave/pull/40  

Now let's add that windows ci build and check if that utf8 stuff actually works on windows... ಠ⌣ಠ  
This is ripped off of boost system (Thanks @pdimov !), let's hope it will work out of the box...

---

## Comment 1

> Username: rlenhardt  
> Created_at: 2018-10-06 15:19:28 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427582080  

Hmmm, only the travis ci build has started. @pdimov is there some github setting to be made to enable the appveyor ci build for wave, including pull requests?

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-10-06 15:20:07 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427582129  

It will but for Appveyor to actually run, someone (by convention the maintainer, Hartmut) needs to activate it from his own Appveyor account. (Unlike Travis, where everything runs under the `boostorg` account, Appveyor uses a different scheme.)

---

## Comment 3

> Username: hkaiser  
> Created_at: 2018-10-06 15:27:21 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427582676  

@pdimov is there a piece of documentation to follow to make that happen?

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-10-06 15:39:39 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427583593  

https://www.appveyor.com/docs/ I suppose. You basically create an account, then add the boostorg/wave project.

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-10-06 15:40:40 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427583664  

Actually you don't even need an account, just click the "Github" button on https://ci.appveyor.com/login :-)

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-10-06 15:42:49 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427583824  

... then click on New Project (https://ci.appveyor.com/projects/new) and choose Wave from the list.

---

## Comment 7

> Username: rlenhardt  
> Created_at: 2018-10-06 16:49:53 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427588842  

I fixed my stupid copy/paste errors in the yml file. I set up appveyor for my forked wave repo and now the build hickups in the depinst.py script: the utf8 paths in the testfiles folder seem to cause problems. :( See here:  
  
https://ci.appveyor.com/project/rlenhardt/wave/build/job/vkxk4qmhhrpwfo35  
  
  
Sorry for the all the commotion, maybe i was to optimistic with the windows ci build...

---

## Comment 8

> Username: pdimov  
> Created_at: 2018-10-06 16:54:53 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427589233  

depinst is mine, I'd fix it if I knew how. Python is not my forte. Suggestions welcome.

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-10-06 17:00:15 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427589692  

This doesn't look like a depinst issue. The directory is just named "utf8-test-ßµ™∃", this may translate to UTF-8 on a POSIX system that doesn't care about what's in the characters, but on Windows you can't just put arbitrary 8 bit codes into the file name and expect to get UTF-8 back. On Windows the names are encoded using UTF-16 and you have to have the actual character you wanted to have there.

---

## Comment 10

> Username: pdimov  
> Created_at: 2018-10-06 17:12:23 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427590729  

Or maybe not. I'll look into that.

---

## Comment 11

> Username: rlenhardt  
> Created_at: 2018-10-06 17:46:29 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427593337  

I can also have a look, but will not come to it until some time next week. If the strange characters in the path make too much problems (i bet there will be other users/tools bitten by the existence of the special include directory) i can also try to create the directory just at test execution and delete it afterwards.

---

## Comment 12

> Username: pdimov  
> Created_at: 2018-10-06 17:52:37 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427593699  

Let's see if https://github.com/boostorg/boostdep/commit/c3058e7c63b6ea1582752847d84056250355f373 fixes it.

---

## Comment 13

> Username: rlenhardt  
> Created_at: 2018-10-06 18:10:28 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427594941  

It's still failing:  
https://ci.appveyor.com/project/rlenhardt/wave/build/1.0.6-wave_utf8/job/455dlgj8k1m9l26h  
  
I guess the fn string that is built after the dir walk and then passed to open in line 92 needs to be wrapped in unicode too.

---

## Comment 14

> Username: pdimov  
> Created_at: 2018-10-06 18:14:45 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427595249  

It's still on a branch, so you're not using it; I'm awaiting the Travis judgment before merging it. Works on Appveyor: https://ci.appveyor.com/project/pdimov/boostdep/builds/19312002  
  
(I've added the same directory to `tools/boostdep/test`.)

---

## Comment 15

> Username: pdimov  
> Created_at: 2018-10-07 00:05:55 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427615121  

depinst is merged to develop now.

---

## Comment 16

> Username: rlenhardt  
> Created_at: 2018-10-07 12:25:52 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-427649375  

After a new build, the directory hickup of depinst is gone now, but some other unrelated errors show up. See here:  
  
https://ci.appveyor.com/project/rlenhardt/wave  
  
The last  toolset combination (gcc from some special mingw distribution) shows the two utf8 tests failing, will look into this later.  
But for the other toolsets I don't know how to proceed, there are some compile errors in boost thread for some toolset combinations, and test run failure in wave lexers for others. I cannot judge if they are real issues or just artefacts of the toolset combination.

---

## Comment 17

> Username: hkaiser  
> Created_at: 2018-10-10 15:09:47 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-428610403  

@rlenhardt what's the status of this now?

---

## Comment 18

> Username: rlenhardt  
> Created_at: 2018-10-10 21:05:16 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-428731592  

@hkaiser: There are seven jobs with different toolset combinations set up in the appveyor ci build, see here for the latest build logs:  
  
https://ci.appveyor.com/project/rlenhardt/wave  
  
VS2013 on windows   
VS2015 on windows %  
VS2017 on windows  
some cygwin gcc on windows %  
some cygwin 64bit gcc on windows %  
some mingw gcc on windows %  
some seh posix mingw gcc on windows *  
  
The toolsets with % are failing to compile some part of the lexer tests, you can check out the logs at  
https://ci.appveyor.com/project/rlenhardt/wave/build/job/gt23i2rndbianiop#L632 for a typical error example -- i don't know what is going on there.  
  
The last toolset with * is actually failing at my new tests -- they cannot include the file in the utf8 directory. I need to find out what actual toolset is used there but won't have time this week and most part of the next week.  
  
I would like to leave open that pull request until i have found out what is going on in the last * toolset, but if you want me to close it, i'm fine with it -- i can do the checks in my forked repository and then open a new pull request when i have found out what is going wrong. But you may want to have a look at the % failures -- maybe they point to real problems in the lexer tests, but maybe they are just artifacts from poorly set up toolsets in these appveyor builds.  
  
I don't know the full details of how the toolsets are set up, maybe @pdimov can give some insights. But after all i don't want to bother you too much with that stuff -- i just wanted to quickly verify that my changes are OK on windows, it seems i have to make some investigations regarding the * errors, but the % errors might just stem from my misunderstanding of the inner workings of the appveyor build.

---

## Comment 19

> Username: hkaiser  
> Created_at: 2018-10-10 21:35:10 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-428740818  

@rlenhardt thanks for the explanations. I have no problems leaving this PR open. Do we have to patch up devel such that all tests pass for now?

---

## Comment 20

> Username: pdimov  
> Created_at: 2018-10-10 22:16:18 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-428752312  

The Cygwin toolsets are failing because of Boost.Thread, it doesn't work there for some reason related to TSS (and there are other problems with Cygwin 64 too). The msvc-14.0 error is unusual (exit status 3 is std::abort), and only in 64 bit, I might take a look at it. MinGW (the ordinary one) fails in ProgramOptions due to a missing `environ`.

---

## Comment 21

> Username: pdimov  
> Created_at: 2018-10-10 22:25:51 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-428754435  

msvc-14.0 works for me for both 32 and 64, so no idea.

---

## Comment 22

> Username: hkaiser  
> Created_at: 2018-10-11 12:44:35 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-428940994  

@pdimov: thanks Peter. I guess we can go ahead and merge this, then.

---

## Comment 23

> Username: pdimov  
> Created_at: 2018-10-11 12:54:36 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-428943998  

There would be no point in testing Cygwin and MinGW when they are failing for reasons unrelated to Wave. So we should probably just keep the first three configurations.

---

## Comment 24

> Username: hkaiser  
> Created_at: 2018-10-11 13:30:34 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-428955880  

@pdimov ok. I'm not familiar with how this is configured nowadays. Is there some documentation explaining things?

---

## Comment 25

> Username: pdimov  
> Created_at: 2018-10-11 13:58:56 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-428966001  

@rlenhardt will update the .yml file.  
  
It seems that Cygwin can be made to compile by adding `threadapi=pthread`, but then it fails with  
  
```  
testwave: could not open input file: C:\boost-git\develop\libs\wave\test\testwave\testfiles\t_1_001.cpp  
```  
  
and it's not clear if we should spend any effort trying to make it work. :-)

---

## Comment 26

> Username: rlenhardt  
> Created_at: 2018-10-17 11:58:13 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-430599711  

I removed the mingw and cygwin builds, and now it is running fine on all remaining visual c++ compilers. See here:  
  
https://ci.appveyor.com/project/rlenhardt/wave/builds/19570322  
  
Regarding setting up appveyor and the wave project: have you succeeded in doing that, @hkaiser? I think you will still have to set some options in the settings page of your appveyor project, for my github fork i still need to trigger builds manually, the github commit trigger is somehow not set up. Can you share the relevant settings here, @pdimov?  
Other than that the current changes should be enough for basic windows building and testing of wave.

---

## Comment 27

> Username: hkaiser  
> Created_at: 2018-10-17 16:02:59 UTC  
> Url: https://github.com/boostorg/wave/pull/40#issuecomment-430688129  

@rlenhardt I have not set it up yet. Thanks for your contributions in any case. I'll go ahead and merge this to devel, then.

---
