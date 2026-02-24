# #269 - [windows, msvc-14.1] debug-store=database: build fails [Open]

> Username: jeking3  
> Created at: 2017-12-01 21:48:52 UTC  
> Updated at: 2021-05-29 18:22:46 UTC  
> Url: https://github.com/boostorg/build/issues/269  

Found: 1.66.0_b1  
  
Adding /FS to cxxflags and linkflags does NOT fix the issue entirely.  
  
{noformat}  
c:\Temp\boost\src\boost_1_66_0>b2 toolset=msvc-14.1 address-model=64 link=static threading=multi variant=debug --build-dir=c:\temp\boost\build\static\debug --prefix=c:\temp\boost\install\static\debug --layout=system debug-store=database -q -j5 install  
  
...  
  
compile-c-c++ c:\temp\boost\build\static\debug\boost\bin.v2\libs\chrono\build\msvc-14.1\debug\address-model-64\debug-store-database\link-static\threadapi-win32\threading-multi\thread_clock.obj  
thread_clock.cpp  
libs\chrono\src\thread_clock.cpp: fatal error C1041: cannot open program database 'c:\temp\boost\build\static\debug\boost\bin.v2\libs\chrono\build\msvc-14.1\debug\address-model-64\debug-store-database\link-static\threadapi-win32\threading-multi\libboost_chrono.pdb'; if multiple CL.EXE write to the same .PDB file, please use /FS  
  
    call "C:\Users\JIM~1.KIN\AppData\Local\Temp\b2_msvc_14.1_vcvarsx86_amd64_.cmd" >nul  
cl /Zm800 -nologo @"c:\temp\boost\build\static\debug\boost\bin.v2\libs\chrono\build\msvc-14.1\debug\address-model-64\debug-store-database\link-static\threadapi-win32\threading-multi\thread_clock.obj.rsp"  
  
...failed compile-c-c++ c:\temp\boost\build\static\debug\boost\bin.v2\libs\chrono\build\msvc-14.1\debug\address-model-64\debug-store-database\link-static\threadapi-win32\threading-multi\thread_clock.obj...  
compile-c-c++ c:\temp\boost\build\static\debug\boost\bin.v2\libs\chrono\build\msvc-14.1\debug\address-model-64\debug-store-database\link-static\threadapi-win32\threading-multi\process_cpu_clocks.obj  
process_cpu_clocks.cpp  
libs\chrono\src\process_cpu_clocks.cpp: fatal error C1041: cannot open program database 'c:\temp\boost\build\static\debug\boost\bin.v2\libs\chrono\build\msvc-14.1\debug\address-model-64\debug-store-database\link-static\threadapi-win32\threading-multi\libboost_chrono.pdb'; if multiple CL.EXE write to the same .PDB file, please use /FS  
  
    call "C:\Users\JIM~1.KIN\AppData\Local\Temp\b2_msvc_14.1_vcvarsx86_amd64_.cmd" >nul  
cl /Zm800 -nologo @"c:\temp\boost\build\static\debug\boost\bin.v2\libs\chrono\build\msvc-14.1\debug\address-model-64\debug-store-database\link-static\threadapi-win32\threading-multi\process_cpu_clocks.obj.rsp"  
  
...failed compile-c-c++ c:\temp\boost\build\static\debug\boost\bin.v2\libs\chrono\build\msvc-14.1\debug\address-model-64\debug-store-database\link-static\threadapi-win32\threading-multi\process_cpu_clocks.obj...  
{noformat}  
  
The build fails as a result.

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-12-06 01:38:22 UTC  
> Url: https://github.com/boostorg/build/issues/269#issuecomment-349501270  

AMDG  
  
  What problems do you get with /FS?  I can replicate  
the problem without /FS, but adding cxxflags=/FS  
was enough to make it work for me in simple cases.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: jeking3  
> Created at: 2017-12-06 15:49:45 UTC  
> Url: https://github.com/boostorg/build/issues/269#issuecomment-349680661  

I was packaging up a complete boost install, so if you apply this same logic on a full build of boost you should see the same behavior I described.  Adding /FS did not allow me to complete a full boost build with the debug-store=database option.  The command line I used is in the description.

---

## Comment 3

> Username: thebrandre  
> Created at: 2019-08-11 16:21:55 UTC  
> Updated at: 2019-08-11 16:48:04 UTC  
> Url: https://github.com/boostorg/build/issues/269#issuecomment-520241033  

I got a similar issue with both msvc-14.1 and 14.2 (latest versions of Visual Studio 2017 and 2019, Community Edition) and boost 1.70.0 on Windows 10.  
  
My first try;  
> call "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Auxiliary\Build\vcvarsall.bat" x64   
> .\bootstrap.bat   
> .\b2 -j8 toolset=msvc-14.2 architecture=x86 address-model=64 runtime-link=shared variant=debug,release debug-symbols=on debug-store=database --without-mpi --without-python stage  
  
Result: half the boost libraries won't build and you'll get lots of errors:   
> if multiple CL.EXE write to the same .PDB file, please use /FS  
  
Next try: add "cflags=/FS" to the arguments of b2 , which gives you  
> libs\math\build\..\src\tr1\cyl_bessel_il.cpp(6):   
error C2859: libboost_math_tr1l-vc142-mt-x64-1_70.pdb is not the pdb file that was used when this precompiled header was created, recreate the precompiled header.  
  
Naturally, I add pch=off to see what happens:  
> compile-c-c++ bin.v2\libs\type_erasure\build\msvc-14.2\release\address-model-64\debug-store-database\debug-symbols-on\link-static\pch-off\threadapi-win32\threading-multi\dynamic_binding.obj  
dynamic_binding.cpp  
failed to write output file 'bin.v2\libs\type_erasure\build\msvc-14.2\release\address-model-64\debug-store-database\debug-symbols-on\link-static\pch-off\threadapi-win32\threading-multi\libboost_type_erasure-vc142-mt-x64-1_70.lib.rsp'!  
  
After that the build just stops in the middle of the process. No further diagnostics.   
  
I guess all of this should be fairly easy to reproduce but I'd be happy to provide further information if it is of any help.  
  
It would be great to have some support for PDBs in boost because the binaries with debug information generated with boost's default /Z7 are insanely large.  
My current workaround is to use cflags=/FS (without pch=off) and ignore the missing binaries for libboost_math_tr1. I copy the pdb files into the library directory via PowerShell because this [issue](https://stackoverflow.com/questions/10127241/how-to-build-boost-library-with-pdb-files-through-b2) seems to still exist.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2019-09-21 18:50:14 UTC  
> Url: https://github.com/boostorg/build/issues/269#issuecomment-533821683  

I can reproduce @thebrandre 's issue locally with msvc-14.2, the problem is basically that Boost.Build uses a different pdb name for each .obj that it builds - this includes when building the pch.  This seems to "magically" just work in the absence of pch's, but when pch's are present the compiler basically expects you to use the same pdb file for both the pch build, and each obj build which uses it.  At least that's what happens if you construct an IDE project using pch's.  
  
Looking at the docs, it looks like you could fix this by specifying a directory but not a filename for the pdb - that assumes that each top level target gets it's own directory?  
  
As noted above, this breaks building the Math lib with debug-store=database.  
  
>Naturally, I add pch=off to see what happens:  
  
>>    compile-c-c++ bin.v2\libs\type_erasure\build\msvc-14.2\release\address-model-64\debug-store-database\debug-symbols-on\link-static\pch-off\threadapi-win32\threading-multi\dynamic_binding.obj  
>>    dynamic_binding.cpp  
>>    failed to write output file 'bin.v2\libs\type_erasure\build\msvc-14.2\release\address-model-64\debug-store-database\debug-symbols-on\link-static\pch-off\threadapi-win32\threading-multi\libboost_type_erasure-vc142-mt-x64-1_70.lib.rsp'!  
  
>After that the build just stops in the middle of the process. No further diagnostics.  
  
It looks like you've hit "windows command line hell" - the command has become too long for the shell to cope with, try building with the `--hash` option as well as `pch=off` and hopefully that should fix things.  BTW building with `pch=off debug-store=database` works for me with no need for the `--hash` option, but I may have been just lucky...

---

## Comment 5

> Username: thebrandre  
> Created at: 2019-09-23 19:20:12 UTC  
> Url: https://github.com/boostorg/build/issues/269#issuecomment-534245308  

> It looks like you've hit "windows command line hell" - the command has become too long for the shell to cope with, try building with the --hash option as well as pch=off and hopefully that should fix things.   
  
Good guess! The option "--hash" did the job. So did building directly in D:.  
Setting that [group policy on Windows 10](https://docs.microsoft.com/en-us/windows/win32/fileio/naming-a-file#maximum-path-length-limitation) that disables the 260 character limit also worked. Now I got three fixes for the pch=off problem. ;-)  
  
To summarize:  
  
- The additional flags `cflags=/FS pch=off` are still required to successfully build boost with `debug-store=database` (tested with boost 1.71).  
  
- Instead of `cflags=/FS`, one might also use a sequential build `-j1`.  
  
- The pdb files are not installed to the stage directory via `b2 stage` or the prefix path via `b2 install` but one can just copy them by hand.

---

## Comment 6

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:16 UTC  
> Url: https://github.com/boostorg/build/issues/269#issuecomment-850877377  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
