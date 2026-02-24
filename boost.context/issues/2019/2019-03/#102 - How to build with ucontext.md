# #102 - How to build with ucontext? [Closed]

> Username: xnox  
> Created at: 2019-03-12 17:21:00 UTC  
> Updated at: 2019-03-14 09:09:15 UTC  
> Closed at: 2019-03-12 17:46:57 UTC  
> Url: https://github.com/boostorg/context/issues/102  

I am trying to build boost with ucontext, such that I could compile context/coroutine/fiber on s390x.  
  
I did following:  
```  
/home/xnox/boost1.67-1.67.0/bjam  -q -d2 context-impl=ucontext --layout=system --ignore-site-config --user-config=/home/xnox/boost1.67-1.67.0/user-config.jam debug-symbols=on  --without-python  
```  
And my user-config.jam has:  
```  
$ cat user-config.jam   
using gcc : : : <compileflags>"-Wdate-time -D_FORTIFY_SOURCE=2" <cflags>"-g -O2 -fdebug-prefix-map=/home/xnox/boost1.67-1.67.0=. -fstack-protector-strong -Wformat -Werror=format-security" <cxxflags>"-g -O2 -fdebug-prefix-map=/home/xnox/boost1.67-1.67.0=. -fstack-protector-strong -Wformat -Werror=format-security -Wno-unused-local-typedefs" <linkflags>"-Wl,-Bsymbolic-functions -Wl,-z,relro -Wl,-z,now" ;  
using mpi ;  
```  
  
Yet compilation fails with:  
```  
    "g++"   -g -O2 -fdebug-prefix-map=/home/xnox/boost1.67-1.67.0=. -fstack-protector-strong -Wformat -Werror=format-security -Wno-unused-local-typedefs -fPIC -pthread -O3 -finline-functions -Wno-inline -Wall -g -Wdate-time -D_FORTIFY_SOURCE=2  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_CONTEXT_SOURCE -DBOOST_DISABLE_ASSERTS -DBOOST_USE_UCONTEXT -DNDEBUG  -I"." -c -o "bin.v2/libs/context/build/gcc-8.3.0/release/context-impl-ucontext/debug-symbols-on/threading-multi/posix/stack_traits.o" "libs/context/src/posix/stack_traits.cpp"  
  
gcc.link.dll bin.v2/libs/context/build/gcc-8.3.0/release/context-impl-ucontext/debug-symbols-on/threading-multi/libboost_context.so.1.67.0  
  
    "g++"    -o "bin.v2/libs/context/build/gcc-8.3.0/release/context-impl-ucontext/debug-symbols-on/threading-multi/libboost_context.so.1.67.0" -Wl,-h -Wl,libboost_context.so.1.67.0 -shared -Wl,--start-group "bin.v2/libs/context/build/gcc-8.3.0/release/context-impl-ucontext/debug-symbols-on/threading-multi/continuation.o" "bin.v2/libs/context/build/gcc-8.3.0/release/context-impl-ucontext/debug-symbols-on/threading-multi/fiber.o" "bin.v2/libs/context/build/gcc-8.3.0/release/context-impl-ucontext/debug-symbols-on/threading-multi/execution_context.o" "bin.v2/libs/context/build/gcc-8.3.0/release/context-impl-ucontext/debug-symbols-on/threading-multi/posix/stack_traits.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -pthread -g -Wl,-Bsymbolic-functions -Wl,-z,relro -Wl,-z,now   
  
/usr/bin/ld: bin.v2/libs/context/build/gcc-8.3.0/release/context-impl-ucontext/debug-symbols-on/threading-multi/fiber.o:(.tbss+0x18): multiple definition of `boost::context::detail::current_rec'; bin.v2/libs/context/build/gcc-8.3.0/release/context-impl-ucontext/debug-symbols-on/threading-multi/continuation.o:(.tbss+0x18): first defined here  
collect2: error: ld returned 1 exit status  
...failed gcc.link.dll bin.v2/libs/context/build/gcc-8.3.0/release/context-impl-ucontext/debug-symbols-on/threading-multi/libboost_context.so.1.67.0...  
...failed updating 1 target...  
...updated 51 targets...  
```  
  
Am I configuring to use ucontext wrong? Is ucontext not available on s390x, or is this a known bug in boost1.67 and I should cherrypick some patches?  
  
Could you provide sample commands to build boost, with context using the ucontext implementation?

---

## Comment 1

> Username: olk  
> Created at: 2019-03-12 17:46:57 UTC  
> Updated at: 2019-03-12 17:49:00 UTC  
> Url: https://github.com/boostorg/context/issues/102#issuecomment-472109108  

As you see in the documentation (https://www.boost.org/doc/libs/1_69_0/libs/context/doc/html/context/architectures.html) - s390x is not supported (needs patches in boost.build).

---

## Comment 2

> Username: xnox  
> Created at: 2019-03-13 13:39:31 UTC  
> Url: https://github.com/boostorg/context/issues/102#issuecomment-472425599  

@olk   
as per that doc  
  
"Note  
If the architecture is not supported but the platform provides ucontext_t, Boost.Context should be compiled with BOOST_USE_UCONTEXT and b2 property context-impl=ucontext.  
"  
And ucontext is provided on s390x....

---

## Comment 3

> Username: xnox  
> Created at: 2019-03-13 13:39:56 UTC  
> Url: https://github.com/boostorg/context/issues/102#issuecomment-472425764  

@olk or for example, how can I build with ucontext on x86_64?

---

## Comment 4

> Username: olk  
> Created at: 2019-03-13 14:22:58 UTC  
> Updated at: 2019-03-13 14:23:15 UTC  
> Url: https://github.com/boostorg/context/issues/102#issuecomment-472442557  

> @olk or for example, how can I build with ucontext on x86_64?  
  
b2 context-impl=ucontext  
  
> And ucontext is provided on s390x....  
  
but boost.build does not support s390x - at least property architecute allows only values:  
 "x86" "ia64" "sparc" "power" "mips1" "mips2" "mips3" "mips4" "mips32" "mips32r2" "mips64" "parisc" "arm" "combined" "combined-x86-power"

---

## Comment 5

> Username: xnox  
> Created at: 2019-03-13 17:06:52 UTC  
> Url: https://github.com/boostorg/context/issues/102#issuecomment-472516105  

I mean s390[x] ports in Debian and Ubuntu, do have boost built since their inspection.... so it does work. But we like use bjam and the like to compile. And there is generic support for s390, because well ./libs/predef/include/boost/predef/architecture/sys390.h exists.  
  
Looking at builtin.py I do agree that it seems severly limited. Given that boost is ported and works fine on many (quite old by now architectures) that are not mentioned there: armhf, arm7, arm64/aarch64/arm8, ppc64le, s390, s390x. At the very least.  
  
Just because boost.architecture doesn't have a setting for it, I wouldn't class that as being critical to the fact that "this arch is not supported", given that clearly there are lots of arches that are supported by boost and not mentioned.  
  
Let me try more at building with ucontext on x86_64 to see if something odd is going on.

---

## Comment 6

> Username: olk  
> Created at: 2019-03-13 17:20:37 UTC  
> Updated at: 2019-03-13 17:22:24 UTC  
> Url: https://github.com/boostorg/context/issues/102#issuecomment-472522115  

> I mean s390[x] ports in Debian and Ubuntu, do have boost built since their inspection.... so it does work.   
  
It does not work for boost.context - property **architecture** is used by boost.context Jamfile.  
Additionally, I consider _supported architecture_ by having asm for the concrete platform.  
  
> Let me try more at building with ucontext on x86_64 to see if something odd is going on.  
  
the unit-tests provided by boost.context already test ucontext on several architectures

---

## Comment 7

> Username: olk  
> Created at: 2019-03-14 09:09:15 UTC  
> Url: https://github.com/boostorg/context/issues/102#issuecomment-472763996  

you are welcome to provide a pull request for boost.build for full support of s390x
