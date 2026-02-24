# #388 - Wrong architecture flags with GCC on x32 [Open]

> Username: giomasce  
> Created at: 2019-01-23 10:34:43 UTC  
> Updated at: 2021-06-11 01:55:28 UTC  
> Url: https://github.com/boostorg/build/issues/388  

Hi, if I build Boost with GCC 8.2 under architecture x32 (i.e., Intel 64 bits used with 32 bits long and pointers, see https://en.wikipedia.org/wiki/X32_ABI) the build fails, because the build system incorrectly puts `-m32` in the compilation flags. The right native mode would be instead `-mx32`, or, even better, no `-m` flag at all and letting the compiler automatically pick the native mode.  
  
In Debian I have to comment line https://github.com/boostorg/build/blob/develop/src/tools/gcc.jam#L440 to build correctly the package. Could you please support this use case?

---

## Comment 1

> Username: giomasce  
> Created at: 2019-01-23 10:35:37 UTC  
> Url: https://github.com/boostorg/build/issues/388#issuecomment-456752427  

See https://wiki.debian.org/X32Port for more information and for how to detect x32 properly.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2019-02-27 03:11:41 UTC  
> Url: https://github.com/boostorg/build/issues/388#issuecomment-467706661  

The gcc documentation states:  
  
> -m32  
> -m64  
> -mx32  
> -m16  
> -miamcu  
> Generate code for a 16-bit, 32-bit or 64-bit environment. The -m32 option sets int, long, and pointer types to 32 bits, and generates code that runs on any i386 system.  
>   
> The -m64 option sets int to 32 bits and long and pointer types to 64 bits, and generates code for the x86-64 architecture. For Darwin only the -m64 option also turns off the -fno-pic and -mdynamic-no-pic options.  
>   
> The -mx32 option sets int, long, and pointer types to 32 bits, and generates code for the x86-64 architecture.  
>   
> The -m16 option is the same as -m32, except for that it outputs the .code16gcc assembly directive at the beginning of the assembly output so that the binary can run in 16-bit mode.  
>   
> The -miamcu option generates code which conforms to Intel MCU psABI. It requires the -m32 option to be turned on.  
  
Hence it seems correct to me to use the `-m32` option to specify 32 bit addressing for portability to any i386 system. The `-mx32` option would indeed use 32 bit addressing, but only on x86_64 systems. If we wanted to support that mixed used case we would need to introduce an `x32` architecture as the current `x86` covers the `x86` and `x86-64` cases. Would that work for your case?

---

## Comment 3

> Username: giomasce  
> Created at: 2019-02-27 08:06:02 UTC  
> Url: https://github.com/boostorg/build/issues/388#issuecomment-467763369  

The point is that I would like Boost to choose the correct flags depending not only on the addressing size, but also on the target ISA, so that X32 users can have a build optimized to their use case. I am not really sure on how to detect this thing with Boost.Build, but ideally Boost should use `-mx32` is the C preprocessor defines _both_ `__ILP32__` and `__x86_64__`, `-m64` if it defines just `__x86_64__` and `-m32` if it defines `__i386__` (plus of course all the other archs as obvious). Would it be possible to integrate this in Boost.Build? In this case, X32 users would automatically have optimized builds without impacting i386 users.

---

## Comment 4

> Username: mirabilos  
> Created at: 2019-08-06 17:15:09 UTC  
> Url: https://github.com/boostorg/build/issues/388#issuecomment-518761990  

`x32` is basically a separate architecture, yes. It shares the x86 common architecture, and the `amd64` (what the GNU people call `x86_64`) CPU architecture, but uses a different ABI… kinda like MIPS has `o32`, `n32` and `n64`, and 32-bit ARM has `arm`, `armel` and `armhf`.  
  
The `x32` architecture is only the first out of several. It would perhaps best be described as `amd64ilp32` as that’s what it is: the `amd64` CPU, but with an ILP32 ABI instead of an LP64 ABI. Incidentally, the `arm64` people are doing the same: there is now a nascent `arm64ilp32` architecture which, last time I saw, was literally named that.  
  
The difference between `x32` and `i386`, or between `arm64ilp32` and `armhf`, is that the former runs in 64-bit mode, has access to 64-bit wide CPU registers, (on x86) more than double the amount of CPU registers (very important!), uses a 64-bit `time_t` and `off_t` and similar types and in general a more modern design. The difference is literally that `long` and pointers (and anything derived thereof, like `size_t`, `ptrdiff_t`, etc.) are restricted to 32 bit (and anything that follows from it, e.g. different syscall numbers on x86). (Another benefit is that the respective kernel runs in full 64-bit mode, so the complete first 4 GiB of the address space, except for a handful of pages for sysenter/sysexit and vDSO, can be used by the application, not just 2/3 GiB like in real 32-bit architectures.) This is similar to the MIPS `n32` architecture, which is a 32-bit one but requires a 64-bit CPU because it uses the calling conventions of `n64`.  
  
    #if defined(__i386__)  
    /* 32-bit ILP32 x86: i386 */  
    #elif defined(__amd64__))  
    #  if defined(__ILP32__) /* ← new! */  
    /* 64-bit ILP32 x86: x32 */  
    #  else  
    /* 64-bit LP64 x86: amd64 */  
    #  endif /* !__ILP32 */  
    #endif /* __amd64__ */  
  
This is how you discover them. The `*64ilp32` architectures have, by consens, the same C præprocessor definitions as the full 64-bit counterparts, plus additionally `__ILP32__` defined.  
  
The GNU autoconf situation is more tricky: basically, you need to pass something like the above through the (host or cross, depending what you need to know) compiler to discover it, but the GNU people recently removed `CC_FOR_BUILD` support, so GNU’s `config.guess` now **always** mis-detects `x32` as `amd64` when it worked before ☹ they try to do tricks like run `file /bin/sh` and parse the output, but that’s not only error-prone but also wrong, plus `*64ilp32` systems are often run in mixed mode where you mix binaries of the 32-bit, *64ilp32, and 64-bit architecture depending on what you need (e.g. an `x32` user might wish to use the 32-bit `i386` JDK to have Hotspot (which is not ported to x32 yet, only the slower Zero JVM), the 64-bit `amd64` PostgreSQL and Qemu (because those need more than 4 GiB RAM), but everything else x32 (for less RAM use = more data per cache line = more speed), and `/bin/sh` might be linked against klibc whose x32 binaries show up as amd64…). But this is a side anecdote which may not be very relevant to you.  
  
Please feel free to ping me (or Daniel Schepler or Adrian Glaubitz or probably Adam Borowski) for any x32 questions.

---

## Comment 5

> Username: mirabilos  
> Created at: 2019-08-06 18:06:59 UTC  
> Url: https://github.com/boostorg/build/issues/388#issuecomment-518781014  

(Ideally you would not pass *any* of `-m32`, `-m64`, `-mx32` but let the compiler use the natively selected one.)

---

## Comment 6

> Username: stale[bot]  
> Created at: 2021-06-11 01:54:53 UTC  
> Url: https://github.com/boostorg/build/issues/388#issuecomment-859203267  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
