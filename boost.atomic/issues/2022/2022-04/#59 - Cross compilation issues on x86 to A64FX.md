# #59 - Cross compilation issues on x86 to A64FX [Closed]

> Username: diehlpk  
> Created at: 2022-04-20 21:35:56 UTC  
> Updated at: 2022-04-27 15:42:11 UTC  
> Closed at: 2022-04-27 15:41:14 UTC  
> Url: https://github.com/boostorg/atomic/issues/59  

Hi,   
  
I am working to compile Boost on Fugaku using the Fujitsu compiler. I could compile boost 1.78 on A64fx successfully, however, with cross compilation, the following error occurs  
  
```  
"./boost/atomic/detail/caps_arch_gcc_aarch64.hpp", line 34: catastrophic error: #error directive: "Boost.Atomic: Failed to determine AArch64 endianness, the target platform is not supported. Please, report to the developers (patches are welcome)."  
  #error "Boost.Atomic: Failed to determine AArch64 endianness, the target platform is not supported. Please, report to the developers (patches are welcome)."  
```  
  
Any ideas why I can compile directly, but not cross compile?

---

## Comment 1

> Username: ivr900  
> Created at: 2022-04-25 23:48:14 UTC  
> Url: https://github.com/boostorg/atomic/issues/59#issuecomment-1109147365  

I guess it is pretty straightforward to build boost on A64FX in the direct mode, the bootstrap.sh catches default environment and the gcc toolset supported by Boost. However, when you run bootstrap.sh on Fugaku login node, I guess that the environment is not A64FX. Cross-compiling is a feature of Fujitsu compilers, which are not in the list of toolsets supported by Boost.   
  
It may require some efforts to make Boost to recognize Fujitsu compilers in proper manner, especially in cross-compiling mode.  I wonder what options do you use when run bootstrap.sh in both cases (direct mode and cross-compiling mode).  
  
Also, most of the boost functionality is in the header-only libraries, which do not require compilation (see https://www.boost.org/doc/libs/1_79_0/more/getting_started/unix-variants.html#header-only-libraries). I wonder if the Boost compilation is required at all for purpose of build of LIBCCHEM. It would be nice to hear a comment on that from LIBCCHEM developers.  
  
Ivan Rostov  
NCI Australia, Canberra

---

## Comment 2

> Username: Lastique  
> Created at: 2022-04-26 00:50:28 UTC  
> Url: https://github.com/boostorg/atomic/issues/59#issuecomment-1109180541  

@diehlpk The error you're seeing is rather descriptive - Boost.Atomic cannot deduce the endianness of your target platform because the compiler does not define any known predefined macros. If it does define some macro that reliably describes target endianness, please let me know and I'll update the check. Sorry, I don't know the Fujitsu compiler, so I can't tell you how to display the predefined macros.  
  
> Any ideas why I can compile directly, but not cross compile?  
  
Obviously, the compiler defines macros differently. No idea why, and this is not expected. Target-related macros, such as endianness, are expected to be defined according to the target platform properties, even when cross-compiling.

---

## Comment 3

> Username: Lastique  
> Created at: 2022-04-26 08:43:21 UTC  
> Url: https://github.com/boostorg/atomic/issues/59#issuecomment-1109521074  

Also, could you test if https://github.com/boostorg/atomic/pull/60 helps?

---

## Comment 4

> Username: Lastique  
> Created at: 2022-04-27 15:42:10 UTC  
> Url: https://github.com/boostorg/atomic/issues/59#issuecomment-1111157808  

I merged the PR. Hopefully, it fixes the problem.
