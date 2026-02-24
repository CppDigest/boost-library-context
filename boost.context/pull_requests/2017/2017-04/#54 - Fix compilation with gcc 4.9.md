# #54 Fix compilation with gcc 4.9. [Closed]

> Username: Lastique  
> Created at: 2017-04-20 20:13:17 UTC  
> Updated at: 2017-04-22 18:57:49 UTC  
> Closed at: 2017-04-22 14:34:50 UTC  
> Url: https://github.com/boostorg/context/pull/54  

The compiler apparently has problems with template ordering, so:  
  
- Remove callcc overloads that have no arguments for the function. These  
  overloads are ambiguous with the overloads taking variadic args. The  
  no-args case is covered by the variadic iverloads anyway.  
  
- Enforce overload resolution with SFINAE. Disable instantiating templated  
  overloads with arguments for which non-templated version of callcc is  
  provided.  
  
- Update friend declarations of callcc in continuation to match the callcc  
  function signatures, including the template arguments. Otherwise, friend  
  declarations declare additional overloads of callcc which ambiguate  
  overload resolution. To do this, the callcc functions have to be  
  forward-declared before the continuation class (default template arguments  
  cannot be specified in friend declarations).  
  
As a result, the compilation succeeds with gcc 4.9.4, but test_callcc.cpp still fails. I'm not familiar with the library to debug the test (my main concern now is compilation).  
  
Tests passed with gcc 6.3.

---

## Comment 1

> Username: DaoWen  
> Created_at: 2017-04-20 22:17:17 UTC  
> Url: https://github.com/boostorg/context/pull/54#issuecomment-295940473  

This is the same problem I ran into in issue #52. I did something similar to get around the compiler error (see DaoWen/context@36a7048c), but then the callcc test failed. I did a bit of debugging in GDB to try to figure out the issue, and it looks like some arguments were getting passed incorrectly to the continuation function. I was going to dig further into this, but it seemed like no one was interested in using gcc-4.9. If this is an issue for you too, then I can probably find time to debug it.

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-04-21 00:12:18 UTC  
> Url: https://github.com/boostorg/context/pull/54#issuecomment-295980600  

> This is the same problem I ran into in issue #52. I did something similar to get around the compiler error (see DaoWen/context@36a7048)  
  
I see. Unfortunately, that one your change alone is not enough as there are multiple sources of ambiguity, as I described in the commit.  
  
> I was going to dig further into this, but it seemed like no one was interested in using gcc-4.9. If this is an issue for you too, then I can probably find time to debug it.  
  
Gcc 4.9 is fairly widespread, it is the default compiler in Debian Jessie, for example. So I'm sure there'll be many interested.  
  
As for me, we're not using Boost.Context in our project currently, but we have to compile Boost packages. That's why my primary concern was compilation. I'm not familiar with Boost.Context, so I'm not sure if and when I'll be able to debug the problem any further. But I think it should be fixed. If you have time to debug, that would certainly be great. Thanks.

---

## Comment 3

> Username: DaoWen  
> Created_at: 2017-04-21 00:25:34 UTC  
> Updated_at: 2017-04-21 00:27:59 UTC  
> Url: https://github.com/boostorg/context/pull/54#issuecomment-295984430  

Are you sure that one-line fix doesn't work for you? I tested that with g++-4.9.4 on x64 Linux and g++-4.9.2 on ppc32 Linux, and that one change fixed the compilation issue for me. (But, like you said, the tests still fail at run time.)  
  
Maybe you can try my patch in PR #55 and see if you still get the compiler errors. If that's not enough to enable you to build the tests, then I can rebase my patch on top of this one, and that should solve the problem.

---

## Comment 4

> Username: Lastique  
> Created_at: 2017-04-21 00:32:26 UTC  
> Url: https://github.com/boostorg/context/pull/54#issuecomment-295986541  

> Are you sure that one-line fix doesn't work for you?  
  
Yes, that was about how I started. The compilation failure I had was not the tests but Boost.Fiber.

---

## Comment 5

> Username: DaoWen  
> Created_at: 2017-04-21 00:45:37 UTC  
> Url: https://github.com/boostorg/context/pull/54#issuecomment-295990486  

> The compilation failure I had was not the tests but Boost.Fiber.  
  
That makes me think the current Boost.Context test coverage is insufficient if these tests pass but there are bugs causing dependent libraries to fail.  
  
I just tried running this, and everything passed:  
  
    ./b2 toolset=gcc-4.9 cxxflags="-std=c++11" --build-dir="/tmp/nick/boost/build" libs/fiber/test  
  
Were you running the Boost.Fiber tests, or was it something else? (I can't reproduce the issue on my end when using #55.)

---

## Comment 6

> Username: DaoWen  
> Created_at: 2017-04-21 00:58:45 UTC  
> Updated_at: 2017-04-21 00:59:04 UTC  
> Url: https://github.com/boostorg/context/pull/54#issuecomment-295994426  

Never mind—I figured out the problem on my end. I was using Boost 1.63 for my main project. I just grabbed 1.64 and now I'm seeing the failures with Boost.Fiber. I'll rebase my change on top of yours and see if that fixes the problems with the Boost.Fiber tests.

---

## Comment 7

> Username: Lastique  
> Created_at: 2017-04-21 00:59:21 UTC  
> Url: https://github.com/boostorg/context/pull/54#issuecomment-295994622  

> Were you running the Boost.Fiber tests, or was it something else?  
  
No, I was trying to compile Boost 1.64.  
  
> I can't reproduce the issue on my end when using #55.  
  
I didn't try the code from #55, but it looks like it fixes the overload resolution with the same one-liner.

---

## Comment 8

> Username: DaoWen  
> Created_at: 2017-04-21 01:13:10 UTC  
> Url: https://github.com/boostorg/context/pull/54#issuecomment-295999064  

I'm not able to build Boost.Fiber with this patch (I'm ignoring the stuff in #55 for now). I keep get this and similar errors over and over again in the output:  
  
```  
./boost/fiber/detail/wrap.hpp: In instantiation of ‘boost::fibers::detail::wrapper<Fn1, Fn2, Tpl>::wrapper(Fn1&&, Fn2&&, Tpl&&) [with Fn1 = boost::fibers::context::context(boost::fibers::worker_context_t, boost::fibers::launch, boost::context::preallocated, StackAlloc, Fn  
&&, Tpl&&) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = void (&)(); Tpl = std::tuple<>]::<lambda(void (*&)(), std::decay<std::tuple<> >::type&, boost::context::continuation&&)>; Fn2 = void (&)(); Tpl = std::tuple<>]’:  
./boost/fiber/detail/wrap.hpp:121:39:   required from ‘boost::fibers::detail::wrapper<Fn1, Fn2, Tpl> boost::fibers::detail::wrap(Fn1&&, Fn2&&, Tpl&&) [with Fn1 = boost::fibers::context::context(boost::fibers::worker_context_t, boost::fibers::launch, boost::context::preallocated, StackAlloc, Fn&&, Tpl&&) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = void (&)(); Tpl = std::tuple<>]::<lambda(void (*&)(), std::decay<std::tuple<> >::type&, boost::context::continuation&&)>; Fn2 = void (&)(); Tpl =  
std::tuple<>]’  
./boost/fiber/context.hpp:375:53:   required from ‘boost::fibers::context::context(boost::fibers::worker_context_t, boost::fibers::launch, boost::context::preallocated, StackAlloc, Fn&&, Tpl&&) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack  
_traits>; Fn = void (&)(); Tpl = std::tuple<>]’  
./boost/fiber/context.hpp:576:70:   required from ‘boost::intrusive_ptr<boost::fibers::context> boost::fibers::make_worker_context(boost::fibers::launch, StackAlloc, Fn&&, Args&& ...) [with StackAlloc = boost::context::basic_fixedsize_stack<boost::context::stack_traits>;  
Fn = void (&)(); Args = {}]’  
./boost/fiber/fiber.hpp:89:110:   required from ‘boost::fibers::fiber::fiber(boost::fibers::launch, std::allocator_arg_t, StackAllocator, Fn&&, Args&& ...) [with StackAllocator = boost::context::basic_fixedsize_stack<boost::context::stack_traits>; Fn = void (&)(); Args =  
{}]’  
./boost/fiber/fiber.hpp:71:73:   required from ‘boost::fibers::fiber::fiber(boost::fibers::launch, Fn&&, Args&& ...) [with Fn = void (&)(); Args = {}; <template-parameter-1-3> = void]’  
libs/fiber/test/test_fiber_post.cpp:149:61:   required from here  
./boost/fiber/detail/wrap.hpp:88:31: error: too many initializers for ‘std::decay<std::tuple<> >::type {aka std::tuple<>}’  
         tpl_{ std::move( tpl) } {  
                               ^  
```  
  
I'm using Boost from the 1.64.0 tarball. Are you using a newer version? We probably want this to compile with the current stable version as well.

---

## Comment 9

> Username: DaoWen  
> Created_at: 2017-04-21 05:36:38 UTC  
> Url: https://github.com/boostorg/context/pull/54#issuecomment-296075854  

OK, the compiler error above was [a totally unrelated issue](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=51747). I was able to build/run all of the Boost.Context and Boost.Fiber tests successfully with g++-4.9.4 using #55 and boostorg/fiber#122.

---

## Comment 10

> Username: Lastique  
> Created_at: 2017-04-21 09:59:07 UTC  
> Url: https://github.com/boostorg/context/pull/54#issuecomment-296149214  

> I'm not able to build Boost.Fiber with this patch  
  
This patch is for develop branch. With it applied (and no other modifications), Boost.Fiber develop compiles successfully for me. My gcc version:  
  
```  
$ g++-4.9 -v  
Using built-in specs.  
COLLECT_GCC=g++-4.9  
COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-linux-gnu/4.9/lto-wrapper  
Target: x86_64-linux-gnu  
Configured with: ../src/configure -v --with-pkgversion='Ubuntu 4.9.4-2ubuntu1' --with-bugurl=file:///usr/share/doc/gcc-4.9/README.Bugs --enable-languages=c,c++,java,go,d,fortran,objc,obj-c++ --prefix=/usr --program-suffix=-4.9 --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --with-gxx-include-dir=/usr/include/c++/4.9 --libdir=/usr/lib --enable-nls --with-sysroot=/ --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --enable-gnu-unique-object --disable-vtable-verify --enable-plugin --with-system-zlib --disable-browser-plugin --enable-java-awt=gtk --enable-gtk-cairo --with-java-home=/usr/lib/jvm/java-1.5.0-gcj-4.9-amd64/jre --enable-java-home --with-jvm-root-dir=/usr/lib/jvm/java-1.5.0-gcj-4.9-amd64 --with-jvm-jar-dir=/usr/lib/jvm-exports/java-1.5.0-gcj-4.9-amd64 --with-arch-directory=amd64 --with-ecj-jar=/usr/share/java/eclipse-ecj.jar --enable-objc-gc --enable-multiarch --disable-werror --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu  
Thread model: posix  
gcc version 4.9.4 (Ubuntu 4.9.4-2ubuntu1)  
```  
  
An adapted patch for Boost 1.64 is here: https://gist.github.com/Lastique/aaf7e3fd2203e24dfab8d355e72201f8.  
  
Now, I admit you might be able to compile Boost.Fiber with lesser changes. But depending on which overloads of `callcc` user's code calls, the problem may reappear. Thus I opted for a more complete solution.

---

## Comment 11

> Username: DaoWen  
> Created_at: 2017-04-21 16:43:56 UTC  
> Url: https://github.com/boostorg/context/pull/54#issuecomment-296242497  

That's weird. I'm using an almost identical version. I think the only difference is that mine's from the xenial release and yours is from jessie?  
  
    $ g++-4.9 --version  
    g++-4.9 (Ubuntu 4.9.4-2ubuntu1~16.04) 4.9.4  
  
> Now, I admit you might be able to compile Boost.Fiber with lesser changes. But depending on which overloads of callcc user's code calls, the problem may reappear. Thus I opted for a more complete solution.  
  
The thing that's confusing about this is that—at least as far as I understand—there is no ambiguity in the unpatched code as per the *expected* C++11 behavior; however, the *actual* behavior with g++-4.9 is buggy, which is what leads to the compiler errors. I think the test suite needs to be expanded to cover all of the different overload cases. We shouldn't be able to produce these types of errors in user code if the test suite passes, since that's kind of the whole point of having a test suite. But, I'm just a random contributor, so unless I make time to write the extra test cases myself, I doubt my ranting about it will make much of a difference.

---

## Comment 12

> Username: Lastique  
> Created_at: 2017-04-21 19:06:36 UTC  
> Url: https://github.com/boostorg/context/pull/54#issuecomment-296280287  

> That's weird. I'm using an almost identical version. I think the only difference is that mine's from the xenial release and yours is from jessie?  
  
The version I quoted above is from Ubuntu 17.04 Zesty. But yes, I also tried to compile 1.64 (the released package) on Debian Jessie - there, the gcc version is 4.9.**2**. I had the same Boost.Fiber compilation failures on Jessie, and these failures were fixed with the patch I posted on gist.  
  
> The thing that's confusing about this is that—at least as far as I understand—there is no ambiguity in the unpatched code as per the expected C++11 behavior; however, the actual behavior with g++-4.9 is buggy  
  
Well, let's just say its behavior is not up to the current standard.  
  
In any case, this is a widespread compiler, and a fairly recent one, too. I think, the library should support it.

---

## Comment 13

> Username: olk  
> Created_at: 2017-04-22 14:34:50 UTC  
> Url: https://github.com/boostorg/context/pull/54#issuecomment-296377303  

ty, I've merged #55

---
