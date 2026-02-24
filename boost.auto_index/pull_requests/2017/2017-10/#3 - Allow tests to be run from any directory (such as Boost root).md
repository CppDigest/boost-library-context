# #3 Allow tests to be run from any directory (such as Boost root) [Merged]

> Username: pdimov  
> Created at: 2017-10-27 14:37:42 UTC  
> Updated at: 2019-03-06 19:41:37 UTC  
> Merged at: 2019-03-05 18:57:32 UTC  
> Closed at: 2019-03-05 18:57:32 UTC  
> Url: https://github.com/boostorg/auto_index/pull/3  

`b2 tools/auto_index/test` from the Boost root fails; this makes it work. I'm not 100% certain that this is the proper fix - the output should in principle go in `bin.v2` and perhaps either a `make` or a `generate` rule should be used - but it seems to do the job.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-03-04 19:30:58 UTC  
> Url: https://github.com/boostorg/auto_index/pull/3#issuecomment-469389300  

Apologies for not noticing this sooner - it seems the tests are broken at present due to a dependency change - once I've fixed that up, I'll set up some CI and then look into this.

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-03-05 21:43:58 UTC  
> Url: https://github.com/boostorg/auto_index/pull/3#issuecomment-469869522  

Still doesn't seem to work (perhaps only with `-j`) because the `_check` tests run before the others, and they need the `.out` files. A `<dependency>` is probably missing.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2019-03-06 17:57:09 UTC  
> Url: https://github.com/boostorg/auto_index/pull/3#issuecomment-470209764  

>Still doesn't seem to work (perhaps only with -j) because the _check tests run before the others, and they need the .out files. A <dependency> is probably missing.  
  
The <dependency> is there but doesn't seem to do anything, I've tried pretty much everything I can think of and it still can't get it to work.  Any ideas?

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-03-06 18:20:19 UTC  
> Url: https://github.com/boostorg/auto_index/pull/3#issuecomment-470217765  

https://github.com/boostorg/auto_index/commit/12925be839ca79610c27a59b8b08bc7035fb1043 works for me locally, let's see that Travis has to say about it.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2019-03-06 18:31:49 UTC  
> Url: https://github.com/boostorg/auto_index/pull/3#issuecomment-470221841  

Still fails for me locally: only passes if I run it twice :(  
  
Also note that the travis script doesn't work yet - the dependencies aren't pulled down correctly - that's next on my list to try and fix.

---

## Comment 6

> Username: pdimov  
> Created_at: 2019-03-06 18:40:02 UTC  
> Url: https://github.com/boostorg/auto_index/pull/3#issuecomment-470224732  

> Still fails for me locally: only passes if I run it twice :(  
  
Be sure to clear bin.v2/tools/auto_index first. If you only delete the .out   
files, the tests that have produced them are still considered to have   
passed.  
  
The correct way to do that is to write a generator that produces the .out   
files, this way they can go in bin.v2 and not pollute the test directory,   
and then the .out files can be dependencies to the check tests, but that's   
too convoluted for me.  
  
> Also note that the travis script doesn't work yet - the dependencies   
> aren't pulled down correctly - that's next on my list to try and fix.  
  
I'm fixing that too.

---

## Comment 7

> Username: pdimov  
> Created_at: 2019-03-06 18:43:51 UTC  
> Url: https://github.com/boostorg/auto_index/pull/3#issuecomment-470226162  

Didn't work. :-(

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2019-03-06 18:50:06 UTC  
> Url: https://github.com/boostorg/auto_index/pull/3#issuecomment-470228522  

No, I still see:  
  
```  
testing.capture-output ..\..\..\bin.v2\tools\auto_index\test\test2.test\msvc-14.1\debug\link-static\threading-multi\test2_check.run  
====== BEGIN OUTPUT ======  
ERROR: Unable to open one or both files.  
  
EXIT STATUS: 2  
====== END OUTPUT ======  
  
  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\..\..\bin.v2\tools\auto_index\test\msvc-14.1\debug\link-static\threading-multi\line_compare_tool.exe" D:\data\boost\boost\tools\auto_index\test/test2.gold D:\data\boost\boost\tools\auto_index\test/test2.out  > "..\..\..\bin.v2\tools\auto_index\test\test2.test\msvc-14.1\debug\link-static\threading-multi\test2_check.output" 2>&1  
    set status=%ERRORLEVEL%  
    echo. >> "..\..\..\bin.v2\tools\auto_index\test\test2.test\msvc-14.1\debug\link-static\threading-multi\test2_check.output"  
    echo EXIT STATUS: %status% >> "..\..\..\bin.v2\tools\auto_index\test\test2.test\msvc-14.1\debug\link-static\threading-multi\test2_check.output"  
    if %status% EQU 0 (  
        copy "..\..\..\bin.v2\tools\auto_index\test\test2.test\msvc-14.1\debug\link-static\threading-multi\test2_check.output" "..\..\..\bin.v2\tools\auto_index\test\test2.test\msvc-14.1\debug\link-static\threading-multi\test2_check.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\..\..\bin.v2\tools\auto_index\test\test2.test\msvc-14.1\debug\link-static\threading-multi\test2_check.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output ..\..\..\bin.v2\tools\auto_index\test\test2.test\msvc-14.1\debug\link-static\threading-multi\test2_check.run...  
```  
  
Which happens even before the auto_index executable is built.

---

## Comment 9

> Username: pdimov  
> Created_at: 2019-03-06 19:10:34 UTC  
> Url: https://github.com/boostorg/auto_index/pull/3#issuecomment-470236256  

Success: https://travis-ci.com/boostorg/auto_index/builds/103418144  
  
Since `$(n)` is just `test1`, I have no idea why this works, whereas the original did not. Well.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2019-03-06 19:26:46 UTC  
> Url: https://github.com/boostorg/auto_index/pull/3#issuecomment-470242302  

:)  
  
Works for me locally too.  Many thanks for that: no way would I have figured that out!

---

## Comment 11

> Username: pdimov  
> Created_at: 2019-03-06 19:41:37 UTC  
> Url: https://github.com/boostorg/auto_index/pull/3#issuecomment-470247454  

The suggestion to use `[ $(t).name ]` came from @grafikrobot, on Slack. :-)

---
