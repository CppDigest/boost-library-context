# #168 - [clang-win] invalid assembler invocation [Open]

> Username: hia3  
> Created at: 2017-02-21 18:37:44 UTC  
> Updated at: 2021-06-26 03:10:23 UTC  
> Url: https://github.com/boostorg/build/issues/168  

Jamroot:  
`exe main : main.asm ;`  
  
main.asm:  
`empty`  
  
command line:  
`b2.exe toolset=clang-win -sBOOST_ROOT=C:\path\to\boost`  
  
error:  
clang-win.compile.asm bin\clang-vc14-win\debug\main.obj  
'-c' is not recognized as an internal or external command,  
operable program or batch file.  
     -c -Zp4 -Cp -Cx  /Zi /Zd /W3  -Fo "bin\clang-vc14-win\debug\main.obj" "main.asm"  
...failed clang-win.compile.asm bin\clang-vc14-win\debug\main.obj...  
  
As you can see, ml.exe (or ml64.exe for address-model=64) is missing in command line.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-01-09 23:35:43 UTC  
> Url: https://github.com/boostorg/build/issues/168#issuecomment-356449487  

The assembler rule is inherited from msvc, but clang-win doesn't actually set it up at all.

---

## Comment 2

> Username: hia3  
> Created at: 2018-01-10 01:22:06 UTC  
> Url: https://github.com/boostorg/build/issues/168#issuecomment-356468650  

Just for the record, I'm not using `toolset=clang-win`. I think this whole idea of `clang-cl` is stupid. I wish this bug-ridden `clang-win.jam` never existed.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:45 UTC  
> Url: https://github.com/boostorg/build/issues/168#issuecomment-868936525  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
