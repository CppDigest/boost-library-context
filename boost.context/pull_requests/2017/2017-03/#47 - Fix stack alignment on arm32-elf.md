# #47 Fix stack alignment on arm32-elf [Merged]

> Username: bdowning  
> Created at: 2017-03-16 20:26:26 UTC  
> Updated at: 2018-03-28 15:42:51 UTC  
> Merged at: 2017-03-17 05:21:55 UTC  
> Closed at: 2017-03-17 05:21:55 UTC  
> Url: https://github.com/boostorg/context/pull/47  

Commit 53e44f632d832f5ccb619c1998d913fba87d94c0 changed "60" to "128" when otherwise adding 64 bytes in make_arm_aapcs_elf_gas.S.  The result of this is that the resulting stack is 4-byte aligned, not 8-byte.  The most notable result of this is that varargs is broken, which pretty much breaks all formatted printing, especially of floats.

---

## Comment 1

> Username: olk  
> Created_at: 2017-03-17 05:21:50 UTC  
> Url: https://github.com/boostorg/context/pull/47#issuecomment-287269698  

realy nice - ty

---

## Comment 2

> Username: olk  
> Created_at: 2017-03-20 17:34:44 UTC  
> Url: https://github.com/boostorg/context/pull/47#issuecomment-287837271  

I've removed your fix because it has caused an segementation fault (tested on ARM hardware).  
Pleae do not submit untested fixes.

---

## Comment 3

> Username: bdowning  
> Created_at: 2017-03-20 17:54:30 UTC  
> Url: https://github.com/boostorg/context/pull/47#issuecomment-287843639  

OK, this works on my platform (on real hardware) and definitely improves things.  Do you have any idea what might be different?  With the currently-commited code, my contexts' stacks are 4-byte aligned, which is in violation of the ARM ABI:  
  
http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.faqs/ka4127.html  
  
```  
arm-v7a-linux-gnueabihf-gcc -v  
Using built-in specs.  
COLLECT_GCC=/opt/OSELAS.Toolchain-2014.12.1/arm-v7a-linux-gnueabihf/gcc-4.9.2-glibc-2.20-binutils-2.24-kernel-3.16-sanitized/bin/arm-v7a-linux-gnueabihf-gcc  
COLLECT_LTO_WRAPPER=/opt/OSELAS.Toolchain-2014.12.1/arm-v7a-linux-gnueabihf/gcc-4.9.2-glibc-2.20-binutils-2.24-kernel-3.16-sanitized/bin/../libexec/gcc/arm-v7a-linux-gnueabihf/4.9.2/lto-wrapper  
Target: arm-v7a-linux-gnueabihf  
Configured with: /home/sander/projekte/debs/trusty/toolchain/OSELAS.Toolchain-2014.12.1/platform-arm-v7a-linux-gnueabihf-gcc-4.9.2-glibc-2.20-binutils-2.24-kernel-3.16-sanitized/build-cross/gcc-4.9.2/configure --build=x86_64-host-linux-gnu --host=x86_64-host-linux-gnu --target=arm-v7a-linux-gnueabihf --with-sysroot=/home/sander/projekte/debs/trusty/toolchain/OSELAS.Toolchain-2014.12.1/inst/opt/OSELAS.Toolchain-2014.12.1/arm-v7a-linux-gnueabihf/gcc-4.9.2-glibc-2.20-binutils-2.24-kernel-3.16-sanitized/sysroot-arm-v7a-linux-gnueabihf --disable-multilib --with-float=hard --with-fpu=vfpv3-d16 --with-arch=armv7-a --with-mode=thumb --with-tune=cortex-a9 --enable-__cxa_atexit --disable-sjlj-exceptions --disable-nls --disable-decimal-float --disable-fixed-point --disable-win32-registry --enable-symvers=gnu --with-pkgversion=OSELAS.Toolchain-2014.12.1 --enable-threads=posix --with-system-zlib --with-gmp=/home/sander/projekte/debs/trusty/toolchain/OSELAS.Toolchain-2014.12.1/platform-arm-v7a-linux-gnueabihf-gcc-4.9.2-glibc-2.20-binutils-2.24-kernel-3.16-sanitized/sysroot-host --with-mpfr=/home/sander/projekte/debs/trusty/toolchain/OSELAS.Toolchain-2014.12.1/platform-arm-v7a-linux-gnueabihf-gcc-4.9.2-glibc-2.20-binutils-2.24-kernel-3.16-sanitized/sysroot-host --with-mpc=/home/sander/projekte/debs/trusty/toolchain/OSELAS.Toolchain-2014.12.1/platform-arm-v7a-linux-gnueabihf-gcc-4.9.2-glibc-2.20-binutils-2.24-kernel-3.16-sanitized/sysroot-host --with-isl=/home/sander/projekte/debs/trusty/toolchain/OSELAS.Toolchain-2014.12.1/platform-arm-v7a-linux-gnueabihf-gcc-4.9.2-glibc-2.20-binutils-2.24-kernel-3.16-sanitized/sysroot-host --with-cloog=/home/sander/projekte/debs/trusty/toolchain/OSELAS.Toolchain-2014.12.1/platform-arm-v7a-linux-gnueabihf-gcc-4.9.2-glibc-2.20-binutils-2.24-kernel-3.16-sanitized/sysroot-host --prefix=/home/sander/projekte/debs/trusty/toolchain/OSELAS.Toolchain-2014.12.1/inst/opt/OSELAS.Toolchain-2014.12.1/arm-v7a-linux-gnueabihf/gcc-4.9.2-glibc-2.20-binutils-2.24-kernel-3.16-sanitized --enable-languages=c,c++ --enable-c99 --enable-long-long --enable-libstdcxx-debug --enable-profile --enable-shared --disable-libssp --enable-checking=release  
Thread model: posix  
gcc version 4.9.2 (OSELAS.Toolchain-2014.12.1)   
```

---

## Comment 4

> Username: bdowning  
> Created_at: 2017-03-20 18:10:19 UTC  
> Url: https://github.com/boostorg/context/pull/47#issuecomment-287848693  

Okay, I do not have a native ARM compiler convenient for my platform, and am unlikely to be able to produce one.  I'm also not finding a good reference on how to cross-compile the boost test suite.  As such, I will have to try to reproduce my issue on an RPi or something like that to proceed.  I'm not sure when I'll have time for that.  
  
Sorry!

---

## Comment 5

> Username: olk  
> Created_at: 2017-03-20 18:33:00 UTC  
> Url: https://github.com/boostorg/context/pull/47#issuecomment-287855614  

No, problem - I own a Odroid-X, but as you I'm too bussy.  
  
AAPCS requires that the stack is aligned to a word boundary (stack % 4 == 0) - the current implementation should do that.  
  
@ shift address in A1 to lower 16 byte boundary  
    bic  a1, a1, #15   
@ reserve space for context-data on context-stack  
    sub  a1, a1, #128  
  
  
I never do cross-compiling the code, so I've no experience with that - sorry.

---

## Comment 6

> Username: bdowning  
> Created_at: 2017-03-20 18:51:43 UTC  
> Url: https://github.com/boostorg/context/pull/47#issuecomment-287861144  

Well, that's strange, since the link I posted (http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.faqs/ka4127.html) also calls out the AAPCS but specifies that it required 8-byte alignment:  
  
> Eight byte stack alignment is a requirement of the Procedure Call Standard for the ARM Architecture [AAPCS]. This specifies that functions must maintain an eight-byte aligned stack address (for example: 0x00, 0x08, 0x10, 0x18, 0x20) on all external interfaces.  
  
Regardless, my gcc's varargs is horribly broken if the stack is not 8-byte aligned.  
  
Here's what I was using to test it:  
```c++  
#include <stdio.h>  
#include <stdarg.h>  
#include <inttypes.h>  
  
#include <boost/context/execution_context_v2.hpp>  
  
static void dumb_float_printer(char *output, ...)  
{  
    va_list ap;  
  
    va_start(ap, output);  
    double foo = va_arg(ap, double);  
    va_end(ap);  
  
    size_t i = 0;  
    for (; i < 10; ++i) {  
        int digit = (int)foo;  
        *output++ = digit + '0';  
        if (i == 0)  
            *output++ = '.';  
        foo -= digit;  
        foo *= 10;  
    }  
    *output = 0;  
}  
  
// static void dumb_float_printer(char *output, double foo)  
// {  
//     size_t i = 0;  
//     for (; i < 10; ++i) {  
//         int digit = (int)foo;  
//         *output++ = digit + '0';  
//         if (i == 0)  
//             *output++ = '.';  
//         foo -= digit;  
//         foo *= 10;  
//     }  
//     *output = 0;  
// }  
  
static void print_foo(const char *context, double foo)  
{  
    union {  
        double f64;  
        uint64_t u64;  
    } u;  
    char dumb[32];  
    u.f64 = foo;  
    dumb_float_printer(dumb, foo);  
    printf("%s:  %24a  %20g  0x%016" PRIx64 "  dumb %s\n", context, u.f64, u.f64, u.u64, dumb);  
    fputs("  dumb: ", stdout); puts(dumb);  
      
}  
  
int main()  
{  
    printf("set up context\n");  
    boost::context::execution_context<double> resume([](boost::context::execution_context<double> && yield, double foo){  
        while (true) {  
            print_foo("task", foo);  
            std::tie(yield, foo) = yield(0.0);  
        }  
        return std::move(yield);  
    });  
    auto frob = [&](double foo) {  
        print_foo("main", foo);  
        std::tie(resume, foo) = resume(foo);  
        puts("");  
    };  
  
    frob(0);  
    frob(1);  
    frob(2);  
    frob(3);  
    frob(3.14159);  
}  
```  
  
The "task" printouts would have bad data; in particular the "raw" float values would be off by 4 bytes, which was what clued me into stack alignment issues.  "dumb_float_printer" would work if you used the version that took a double, but would fail if you used the version that took varargs.

---

## Comment 7

> Username: olk  
> Created_at: 2017-03-20 19:34:15 UTC  
> Url: https://github.com/boostorg/context/pull/47#issuecomment-287872803  

I'm using document IHI0042D (Procedure Call Standrad for the ARM Architecture), section 5.2.1.1 'Universal stack contraints': 'SP mod 4 = 0. The stack must at all times be aligned to a word boundary.'  
  
I refer to the code in branch develop - the lib contains a unit-test that use sscanf().

---

## Comment 8

> Username: bdowning  
> Created_at: 2018-03-28 15:42:51 UTC  
> Url: https://github.com/boostorg/context/pull/47#issuecomment-376934250  

I see that this same fix wound up reapplied about a month later in a different commit (https://github.com/boostorg/context/commit/64d8d1445b46fe27042af165749538ce79b674fa); not sure why you had a failure at the time.  Anyway, glad it's fixed.

---
