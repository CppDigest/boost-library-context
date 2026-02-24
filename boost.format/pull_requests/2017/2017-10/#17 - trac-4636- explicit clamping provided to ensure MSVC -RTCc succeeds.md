# #17 trac-4636: explicit clamping provided to ensure MSVC /RTCc succeeds [Merged]

> Username: jeking3  
> Created at: 2017-10-12 01:01:01 UTC  
> Updated at: 2017-10-18 19:25:55 UTC  
> Merged at: 2017-10-12 16:18:01 UTC  
> Closed at: 2017-10-12 16:18:01 UTC  
> Url: https://github.com/boostorg/format/pull/17  

This resolves a boost trac issue that has been open for 7 years!  
  
Appveyor: https://ci.appveyor.com/project/jeking3/format/build/1.0.5-develop  
Travis: https://travis-ci.org/jeking3/format/builds/286796222

---

## Comment 1

> Username: mclow  
> Created_at: 2017-10-12 01:22:01 UTC  
> Updated_at: 2017-10-12 01:22:15 UTC  
> Url: https://github.com/boostorg/format/pull/17#issuecomment-335993038  

What's the trac issue #?  -- Never mind!

---

## Comment 2

> Username: mclow  
> Created_at: 2017-10-12 01:24:49 UTC  
> Url: https://github.com/boostorg/format/pull/17#issuecomment-335993430  

Of course, the very first comment in the trac is "FWIW, I advise against enabling run-time checks, since the result is non-conformant. The behavior of this cast is well defined according to the standard."  
  
I'll be needling the MS people about this, since they haven't fixed their runtime in seven years.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-10-12 01:32:38 UTC  
> Url: https://github.com/boostorg/format/pull/17#issuecomment-335994467  

@mclow If you build with MSVC 14.0 or later, you get an error from the standard library embedded with the product.  Regardless, the bug was open for 7 years and the fix was trivial, and it was a useful exercise for me to learn a little bit about the library's inner workings.  
  
```  
c:\boost\libs\format\test>..\..\..\b2 toolset=msvc-14.0 cxxflags="/RTCsuc" -q  
Performing configuration checks  
  
    - 32-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
...patience...  
...found 2310 targets...  
...updating 81 targets...  
compile-c-c++ ..\..\..\bin.v2\libs\format\test\format_test1.test\msvc-14.0\debug\threadapi-win32\threading-multi\format_test1.obj  
format_test1.cpp  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\yvals.h(112): error C2338: /RTCc rejects conformant code, so it isn't supported by the C++ Standard Library. Either remove this compiler option, or define _ALLOW_RTCc_IN_STL to acknowledge that you have received this warning.  
  
    call "C:\Users\JIM~1.KIN\AppData\Local\Temp\b2_msvc_14.0_vcvarsall_x86.cmd" >nul  
cl /Zm800 -nologo @"..\..\..\bin.v2\libs\format\test\format_test1.test\msvc-14.0\debug\threadapi-win32\threading-multi\format_test1.obj.rsp"  
  
...failed compile-c-c++ ..\..\..\bin.v2\libs\format\test\format_test1.test\msvc-14.0\debug\threadapi-win32\threading-multi\format_test1.obj...  
...failed updating 1 target...  
```

---

## Comment 4

> Username: jeking3  
> Created_at: 2017-10-12 02:15:10 UTC  
> Url: https://github.com/boostorg/format/pull/17#issuecomment-336000359  

@mclow could you take a quick look at https://svn.boost.org/trac10/ticket/4658 and double-check my test logic?

---

## Comment 5

> Username: jeking3  
> Created_at: 2017-10-12 13:34:36 UTC  
> Url: https://github.com/boostorg/format/pull/17#issuecomment-336138827  

@mclow @eldiener any more actions I need to take to move this forward?

---

## Comment 6

> Username: mclow  
> Created_at: 2017-10-12 14:05:05 UTC  
> Url: https://github.com/boostorg/format/pull/17#issuecomment-336147580  

I would have closed this years ago as "not a bug" (more accurately, "not a boost bug"), but if you don't want to do this, I think this patch will shut up the MS runtime.  It adds code and execution time, but not much, and this is Boost.Format, which is not a high-performance library.

---

## Comment 7

> Username: mclow  
> Created_at: 2017-10-12 14:12:47 UTC  
> Url: https://github.com/boostorg/format/pull/17#issuecomment-336149857  

For Trac 4658, you could also do %d with `unsigned char` and %u with `char`.  (though I can't get the OP's examples to give an error)

---

## Comment 8

> Username: eldiener  
> Created_at: 2017-10-12 14:40:26 UTC  
> Url: https://github.com/boostorg/format/pull/17#issuecomment-336158212  

This patch looks good to me.

---
