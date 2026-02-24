# #580 Port TuttleOFX extensions : align, pattern, shrink [Closed]

> Username: meshtag  
> Created at: 2021-03-16 10:24:45 UTC  
> Updated at: 2021-03-17 09:05:40 UTC  
> Closed at: 2021-03-17 04:30:04 UTC  
> Url: https://github.com/boostorg/gil/pull/580  

### Description  
  
This pull request intends to port three TuttleOFX extensions from [TuttleOFX repository](https://github.com/tuttleofx/TuttleOFX/tree/develop/libraries/boostHack/boost/gil/extension/toolbox) as mentioned in #449 .  
I have tried not to change anything significant in actual extension code. However, I have reformatted the test cases completely in order to align them with current practices followed by Gil.  
  
I would like to confirm the following points about some TuttleOFX extensions :   
 - Is it reasonable to port ```line``` extension ? It is used for drawing only vertical and horizontal lines which can be done with   
   existing rasterisation functionality.  
- ```wuline``` extension uses Wu's line algorithm to draw lines. Since this functionality is already developed using Bresenham line   
  algorithm, is there a need to port it ?  
- I am not sure about the usefulness of ```layer``` extension in Gil.  
  
  
### Tasklist  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2021-03-16 18:59:23 UTC  
> Url: https://github.com/boostorg/gil/pull/580#issuecomment-800525267  

@meshtag Thanks for your work.  
  
I'd keep this PR focused on the three: align, pattern and shrink.  
The other three (Wu, line and layer) can be considered in future.  
It's more important to have new additions covered with tests and pass the CI builds.

---

## Comment 2

> Username: meshtag  
> Created_at: 2021-03-16 19:51:27 UTC  
> Url: https://github.com/boostorg/gil/pull/580#issuecomment-800558036  

Build results are pretty strange, builds are successful with clang but are failing with gcc. Any idea what can be the reason? (@mloskot )

---

## Comment 3

> Username: mloskot  
> Created_at: 2021-03-16 20:14:34 UTC  
> Updated_at: 2021-03-16 20:14:57 UTC  
> Url: https://github.com/boostorg/gil/pull/580#issuecomment-800571816  

Tests of the `align` seem failing for the optimised (release) builds (see [raw log](https://pipelines.actions.githubusercontent.com/NNaoc1sCOUAja8lj0Hi42nHgjNcp5iB57crkeWrCFWEOjiyhdm/_apis/pipelines/1/runs/207/signedlogcontent/16?urlExpires=2021-03-16T20%3A07%3A49.6752293Z&urlSigningMethod=HMACV1&urlSignature=bfsqyQkkZ0RnLYwwwLUUIDwysxGBW2176yZoKfnB6jI%3D)):  
  
```  
2021-03-16T19:10:33.7778759Z gcc.compile.c++ bin.v2/libs/gil/test/extension/toolbox/aligned.test/gcc-6/release/cxxstd-14-iso/threading-multi/visibility-hidden/aligned.o  
2021-03-16T19:10:33.9397110Z gcc.link bin.v2/libs/gil/test/extension/toolbox/aligned.test/gcc-6/release/cxxstd-14-iso/threading-multi/visibility-hidden/aligned  
2021-03-16T19:10:33.9752440Z testing.capture-output bin.v2/libs/gil/test/extension/toolbox/aligned.test/gcc-6/release/cxxstd-14-iso/threading-multi/visibility-hidden/aligned.run  
2021-03-16T19:10:33.9754074Z ====== BEGIN OUTPUT ======  
2021-03-16T19:10:33.9755992Z libs/gil/test/extension/toolbox/aligned.cpp(288): test 'gil::equal_pixels(obtained_center_middle_view, gil::view(expected_center_middle_img))' failed in function 'int main()'  
2021-03-16T19:10:33.9758348Z libs/gil/test/extension/toolbox/aligned.cpp(289): test 'gil::equal_pixels(obtained_left_top_view, gil::view(expected_left_top_img))' failed in function 'int main()'  
2021-03-16T19:10:33.9759548Z 2 errors detected.  
2021-03-16T19:10:33.9760052Z   
2021-03-16T19:10:33.9760619Z EXIT STATUS: 2  
2021-03-16T19:10:33.9761207Z ====== END OUTPUT ======  
2021-03-16T19:10:34.0033218Z   
2021-03-16T19:10:34.0034394Z     LD_LIBRARY_PATH="/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
2021-03-16T19:10:34.0035282Z export LD_LIBRARY_PATH  
2021-03-16T19:10:34.0035788Z   
2021-03-16T19:10:34.0036309Z     status=0  
2021-03-16T19:10:34.0037701Z     if test $status -ne 0 ; then  
2021-03-16T19:10:34.0038697Z         echo Skipping test execution due to testing.execute=off  
2021-03-16T19:10:34.0039506Z         exit 0  
2021-03-16T19:10:34.0040033Z     fi  
2021-03-16T19:10:34.0042320Z      "bin.v2/libs/gil/test/extension/toolbox/aligned.test/gcc-6/release/cxxstd-14-iso/threading-multi/visibility-hidden/aligned"   > "bin.v2/libs/gil/test/extension/toolbox/aligned.test/gcc-6/release/cxxstd-14-iso/threading-multi/visibility-hidden/aligned.output" 2>&1 < /dev/null  
2021-03-16T19:10:34.0044138Z     status=$?  
2021-03-16T19:10:34.0045694Z     echo >> "bin.v2/libs/gil/test/extension/toolbox/aligned.test/gcc-6/release/cxxstd-14-iso/threading-multi/visibility-hidden/aligned.output"  
2021-03-16T19:10:34.0047984Z     echo EXIT STATUS: $status >> "bin.v2/libs/gil/test/extension/toolbox/aligned.test/gcc-6/release/cxxstd-14-iso/threading-multi/visibility-hidden/aligned.output"  
2021-03-16T19:10:34.0049709Z     if test $status -eq 0 ; then  
2021-03-16T19:10:34.0052055Z         cp "bin.v2/libs/gil/test/extension/toolbox/aligned.test/gcc-6/release/cxxstd-14-iso/threading-multi/visibility-hidden/aligned.output" "bin.v2/libs/gil/test/extension/toolbox/aligned.test/gcc-6/release/cxxstd-14-iso/threading-multi/visibility-hidden/aligned.run"  
2021-03-16T19:10:34.0053968Z     fi  
2021-03-16T19:10:34.0054534Z     verbose=0  
2021-03-16T19:10:34.0055371Z     if test $status -ne 0 ; then  
2021-03-16T19:10:34.0056768Z         verbose=1  
2021-03-16T19:10:34.0057330Z     fi  
2021-03-16T19:10:34.0058150Z     if test $verbose -eq 1 ; then  
2021-03-16T19:10:34.0058783Z         echo ====== BEGIN OUTPUT ======  
2021-03-16T19:10:34.0060251Z         cat "bin.v2/libs/gil/test/extension/toolbox/aligned.test/gcc-6/release/cxxstd-14-iso/threading-multi/visibility-hidden/aligned.output"  
2021-03-16T19:10:34.0061418Z         echo ====== END OUTPUT ======  
2021-03-16T19:10:34.0062142Z     fi  
2021-03-16T19:10:34.0062603Z     exit $status  
2021-03-16T19:10:34.0063209Z   
2021-03-16T19:10:34.0064855Z ...failed testing.capture-output bin.v2/libs/gil/test/extension/toolbox/aligned.test/gcc-6/release/cxxstd-14-iso/threading-multi/visibility-hidden/aligned.run...  
```  
  
Interestingly, it is also failing w/ MSVC for non-optimised (debug) build on AppVeyor (see [log](https://ci.appveyor.com/project/mloskot/boostorg-gil/builds/38254712/job/k02j7f6dclop9nfe)):  
  
```  
[00:05:50] compile-c-c++ bin.v2\libs\gil\test\extension\toolbox\aligned.test\msvc-14.1\dbg\adrs-mdl-32\cxstd-14-iso\thrd-mlt\aligned.obj  
[00:05:50] aligned.cpp  
[00:05:50] msvc.link bin.v2\libs\gil\test\extension\toolbox\aligned.test\msvc-14.1\dbg\adrs-mdl-32\cxstd-14-iso\thrd-mlt\aligned.exe  
[00:05:50] ...on 400th target...  
[00:05:50] testing.capture-output bin.v2\libs\gil\test\extension\toolbox\aligned.test\msvc-14.1\dbg\adrs-mdl-32\cxstd-14-iso\thrd-mlt\aligned.run  
[00:05:50] ====== BEGIN OUTPUT ======  
[00:05:50] libs\gil\test\extension\toolbox\aligned.cpp(288): test 'gil::equal_pixels(obtained_center_middle_view, gil::view(expected_center_middle_img))' failed in function 'int __cdecl main(void)'  
[00:05:50] libs\gil\test\extension\toolbox\aligned.cpp(289): test 'gil::equal_pixels(obtained_left_top_view, gil::view(expected_left_top_img))' failed in function 'int __cdecl main(void)'  
[00:05:50] 2 errors detected.  
[00:05:50]    
[00:05:50] EXIT STATUS: 2   
[00:05:50] ====== END OUTPUT ======  
[00:05:50]   
[00:05:50]       
[00:05:50]     set status=0  
[00:05:50]     if %status% NEQ 0 (  
[00:05:50]         echo Skipping test execution due to testing.execute=off  
[00:05:50]         exit 0  
[00:05:50]     )  
[00:05:50]      "bin.v2\libs\gil\test\extension\toolbox\aligned.test\msvc-14.1\dbg\adrs-mdl-32\cxstd-14-iso\thrd-mlt\aligned.exe"   > "bin.v2\libs\gil\test\extension\toolbox\aligned.test\msvc-14.1\dbg\adrs-mdl-32\cxstd-14-iso\thrd-mlt\aligned.output" 2>&1   
[00:05:50]     set status=%ERRORLEVEL%  
[00:05:50]     echo. >> "bin.v2\libs\gil\test\extension\toolbox\aligned.test\msvc-14.1\dbg\adrs-mdl-32\cxstd-14-iso\thrd-mlt\aligned.output"  
[00:05:50]     echo EXIT STATUS: %status% >> "bin.v2\libs\gil\test\extension\toolbox\aligned.test\msvc-14.1\dbg\adrs-mdl-32\cxstd-14-iso\thrd-mlt\aligned.output"  
[00:05:50]     if %status% EQU 0 (  
[00:05:50]         copy "bin.v2\libs\gil\test\extension\toolbox\aligned.test\msvc-14.1\dbg\adrs-mdl-32\cxstd-14-iso\thrd-mlt\aligned.output" "bin.v2\libs\gil\test\extension\toolbox\aligned.test\msvc-14.1\dbg\adrs-mdl-32\cxstd-14-iso\thrd-mlt\aligned.run"  
[00:05:50]     )  
[00:05:50]     set verbose=0  
[00:05:50]     if %status% NEQ 0 (  
[00:05:50]         set verbose=1  
[00:05:50]     )  
[00:05:50]     if %verbose% EQU 1 (  
[00:05:50]         echo ====== BEGIN OUTPUT ======  
[00:05:50]         type "bin.v2\libs\gil\test\extension\toolbox\aligned.test\msvc-14.1\dbg\adrs-mdl-32\cxstd-14-iso\thrd-mlt\aligned.output"  
[00:05:50]         echo ====== END OUTPUT ======  
[00:05:50]     )  
[00:05:50]     exit %status%  
[00:05:50]   
[00:05:50] ...failed testing.capture-output bin.v2\libs\gil\test\extension\toolbox\aligned.test\msvc-14.1\dbg\adrs-mdl-32\cxstd-14-iso\thrd-mlt\aligned.run...  
```  
  
I suspect some kind of undefined behaviour, impossible to tell without debugging, I'm afraid.  
  
------  
  
I'd suggest you to close this PR and split it into three separate PRs:  
- pattern  
- shrink  
- align  
  
Then, we will merge the ones that pass and the `align` can wait until you/we/whoever have time to have a look.

---

## Comment 4

> Username: meshtag  
> Created_at: 2021-03-17 04:31:47 UTC  
> Url: https://github.com/boostorg/gil/pull/580#issuecomment-800785717  

Alright, I will split them and create new PRs

---

## Comment 5

> Username: meshtag  
> Created_at: 2021-03-17 04:51:43 UTC  
> Updated_at: 2021-03-17 05:20:32 UTC  
> Url: https://github.com/boostorg/gil/pull/580#issuecomment-800792246  

I messed up while rebasing, apologies for that. I will try not to repeat such a mistake in future. Is there anything I can do to clear the mess I created? Apologies again

---

## Comment 6

> Username: mloskot  
> Created_at: 2021-03-17 09:05:40 UTC  
> Url: https://github.com/boostorg/gil/pull/580#issuecomment-800918397  

@meshtag Don't worry, This PR goes to bin anyway.

---
