# #33 - When compile test in process lib for Boost, It will pop out a box and hang. [Closed]

> Username: shanshan0309  
> Created at: 2018-03-07 06:38:14 UTC  
> Updated at: 2019-04-10 02:17:58 UTC  
> Closed at: 2019-04-10 02:17:58 UTC  
> Url: https://github.com/boostorg/process/issues/33  

We tried to build and run process test for Boost. It will pop out a box and hang. Could you please help take a look at this? Thanks!  
  
**Reproduce steps:**  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. Open a VS 2015 x86 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\Release --address-model=32  
5. .\b2 variant=release --build-dir=..\out\Release --address-model=32  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\process\test  
  
**Expected result:**  
No issue  
  
**Actual result:**  
Pop out a box(sparring_partner.exe has stopped working) and hang.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-03-07 12:21:50 UTC  
> Url: https://github.com/boostorg/process/issues/33#issuecomment-371121740  

Well sparring_partner is used by most of the tests, that's launched as the process tested against. Thus the test launching it hangs. I can't make out the issue there. It might be that the dynamic linking is not working properly; I just call `b2` from `libs/process/test` directly and it would build all dependencies. Which I guess would be a bug, but not as critical I think. Could you check if the bug occurs then as well?

---

## Comment 2

> Username: shanshan0309  
> Created at: 2018-03-12 09:55:54 UTC  
> Url: https://github.com/boostorg/process/issues/33#issuecomment-372252614  

Thanks for looking into this issue, @klemens-morgenstern.   
  
1. When execute "mt -nologo -manifest "..\out\x86rel\boost\bin.v2\libs\process\test\async.test\msvc-14.1\release\threadapi-win32\threading-multi\async.exe.manifest" "-outputresource:..\out\x86rel\boost\bin.v2\libs\process\test\async.test\msvc-14.1\release\threadapi-win32\threading-multi\async.exe;1", it will pop out first box.   
2. When execute "mt -nologo -manifest "..\out\x86rel\boost\bin.v2\libs\process\test\exit_code.test\msvc-14.1\release\threading-multi\exit_code.exe.manifest" "-outputresource:..\out\x86rel\boost\bin.v2\libs\process\test\exit_code.test\msvc-14.1\release\threading-multi\exit_code.exe;1"", it will pop out second box.   
But these will not cause hang. It will continue to run.  
  
When execute "..\out\x86rel\boost\bin.v2\libs\process\test\async_system_stackless.test\msvc-14.1\release\link-static\threadapi-win32\threading-multi\async_system_stackless.test", it will hang.  
  
Thanks!

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2018-03-12 10:23:14 UTC  
> Url: https://github.com/boostorg/process/issues/33#issuecomment-372260326  

Well my first guess would be that the sparring-partner does not find required dlls on launch. That is hard to debug (let alone remotely) but you might be able to find that out with the depencywalker and the put them in thr same folder as.the executable or into thr path variable.  
Can you manually start sparring-partner.exe?

---

## Comment 4

> Username: shanshan0309  
> Created at: 2018-03-12 10:36:45 UTC  
> Url: https://github.com/boostorg/process/issues/33#issuecomment-372263936  

Thanks. Yeah, you are right.  The sparring-partner does not find required dlls on launch. When I start sparring-partner.exe, it will pop out a box that shows: "The program can't start because boost_filesystem-vc141-mt-x32-1_67.dll is missing from your computer. Try reinstalling the program to fix this problem."

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2018-03-13 02:07:57 UTC  
> Url: https://github.com/boostorg/process/issues/33#issuecomment-372522364  

They are somewhere in the build_dir, which is a real pain to fix. Which is why I don't use a custom build-dir to begin with. Just try building to the default location and run b2 in the libs/process/test and that might fix it.

---

## Comment 6

> Username: shanshan0309  
> Created at: 2018-03-13 05:46:11 UTC  
> Url: https://github.com/boostorg/process/issues/33#issuecomment-372554053  

Hi, Thanks @klemens-morgenstern. However, when I tried to build to the default location and run b2 in the process lib and still hit this issue.

---

## Comment 7

> Username: shanshan0309  
> Created at: 2018-03-15 02:18:30 UTC  
> Url: https://github.com/boostorg/process/issues/33#issuecomment-373238225  

Hello, @klemens-morgenstern. Any update for this? Thank you!

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2018-03-19 05:46:03 UTC  
> Url: https://github.com/boostorg/process/issues/33#issuecomment-374107459  

Sry, didn't get to answering issues, Malaysia is bloody distracting. So I run the test in by calling `b2` in 'boost/libs/process/test'  and if there is a path issue with your build, I don't know how to solve it, because that is tbe build system not putting them into `PATH`. You can try to put whereever your boost binaries are into the `PATH` folder. I don't think it's really possible to debug your build setup remotely though.

---

## Comment 9

> Username: shanshan0309  
> Created at: 2018-03-23 09:33:24 UTC  
> Url: https://github.com/boostorg/process/issues/33#issuecomment-375593889  

@klemens-morgenstern, thanks. When I put the boost binaries are into the PATH but still hit the error. I’m confused about this. ☹☹

---

## Comment 10

> Username: klemens-morgenstern  
> Created at: 2018-03-26 16:44:17 UTC  
> Url: https://github.com/boostorg/process/issues/33#issuecomment-376232457  

So am I. Can you launch the `sparring_partner.exe` directly without the error?

---

## Comment 11

> Username: shanshan0309  
> Created at: 2018-03-27 09:18:19 UTC  
> Url: https://github.com/boostorg/process/issues/33#issuecomment-376455081  

Hi @klemens-morgenstern.   
1. There is no error when I execute "..\out\x64rel\boost\bin.v2\libs\process\test\msvc-14.1\release\threading-multi\sparring_partner.exe" individually.   
2. When execute ""..\out\x64rel\boost\bin.v2\libs\process\test\async.test\msvc-14.1\release\threadapi-win32\threading-multi\async.exe"  "..\out\x64rel\boost\bin.v2\libs\process\test\msvc-14.1\release\threading-multi\sparring_partner.exe"", it will pop out a box and shows that "sparring_partner.exe has stopped working".

---

## Comment 12

> Username: klemens-morgenstern  
> Created at: 2019-04-10 02:17:58 UTC  
> Url: https://github.com/boostorg/process/issues/33#issuecomment-481507204  

I think this can happen when abinary on windows calls `std::abort` - which `sparring_partner` does as a test. Since this is the child program, that doesn't matter, you can just click ignore.
