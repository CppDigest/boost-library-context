# #106 Revert iconv check executable related changes [Closed]

> Username: TheSalvator  
> Created at: 2022-09-06 17:48:23 UTC  
> Updated at: 2022-09-12 09:36:20 UTC  
> Closed at: 2022-09-12 09:24:18 UTC  
> Url: https://github.com/boostorg/locale/pull/106  

fixes #63

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2022-09-11 03:58:07 UTC  
> Url: https://github.com/boostorg/locale/pull/106#issuecomment-1242880404  

This looks good to me.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2022-09-11 09:44:13 UTC  
> Url: https://github.com/boostorg/locale/pull/106#issuecomment-1242927834  

Thanks for this! After discussion in Slack with @grafikrobot I created #107 which also fixes a similar issue with the ICU check. I also improved the naming and added comments on why the `obj` rule is required.  
  
I'd suspect that the issue is only there when you pass `ICONV_PATH`, i.e. when you need an additional include path. Is that correct?

---

## Comment 3

> Username: TheSalvator  
> Created_at: 2022-09-12 09:24:18 UTC  
> Url: https://github.com/boostorg/locale/pull/106#issuecomment-1243457690  

@Flamefire Yep you can see it in here  
https://c3i.jfrog.io/c3i/misc/logs/pr/12807/5-configs/macos-clang/boost/1.80.0//6dce47697105e9cab74f0925ce72b345cce34916-build.txt  
  
`ICONV_PATH=/Users/jenkins/w/prod/BuildSingleReference@4/.conan/data/libiconv/1.17/_/_/package/0aa7be2d0914b6f3128105e305c90b9cb0cae86d`  
  
Closing this one in favor of #107

---

## Comment 4

> Username: Flamefire  
> Created_at: 2022-09-12 09:28:08 UTC  
> Url: https://github.com/boostorg/locale/pull/106#issuecomment-1243462430  

I see thanks! Were you able to test #107 ? I don't have any system to actually test this and the Github OSX runners all have the iconv stuff in libc so the issue isn't reproducible there and hence I can't verify the fix.

---

## Comment 5

> Username: TheSalvator  
> Created_at: 2022-09-12 09:36:20 UTC  
> Url: https://github.com/boostorg/locale/pull/106#issuecomment-1243472660  

> I see thanks! Were you able to test #107 ? I don't have any system to actually test this and the Github OSX runners all have the iconv stuff in libc so the issue isn't reproducible there and hence I can't verify the fix.  
  
Not yet, but today i'll do it)

---
