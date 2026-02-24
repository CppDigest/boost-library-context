# #29 - addressof using operator& on Ubuntu's gcc-4.7 in C++11 mode [Closed]

> Username: danieljames  
> Created at: 2017-04-20 09:01:26 UTC  
> Updated at: 2017-04-20 16:19:40 UTC  
> Closed at: 2017-04-20 16:19:40 UTC  
> Url: https://github.com/boostorg/core/issues/29  

I'm getting `‘operator&’ not defined` compile errors from `addressof` on Ubuntu's GCC 4.7.4. We don't have any test runners using 4.7.4, so I guess this is specific to the exact version. Core from Boost 1.63.0 doesn't have this error.  
  
Here's the first error:  
  
    $ "g++-4.7" "--std=c++0x" -I ../../../ addressof_test.cpp 2>&1 > addressof.output  
    In file included from ../../../boost/utility/addressof.hpp:15:0,  
                     from addressof_test.cpp:11:  
    ../../../boost/core/addressof.hpp: In instantiation of ‘constexpr const bool boost::detail::addressof_is_constexpr<char>::value’:  
    ../../../boost/core/addressof.hpp:247:1:   required by substitution of ‘template<class T> constexpr typename boost::detail::addressof_if<boost::detail::addressof_is_constexpr<T>::value, T>::type boost::detail::addressof(T&) [with T = char]’  
    ../../../boost/core/addressof.hpp:258:31:   required from ‘constexpr T* boost::addressof(T&) [with T = char]’  
    addressof_test.cpp:30:5:   required from ‘void scalar_test(T*) [with T = char]’  
    addressof_test.cpp:83:23:   required from here  
    ../../../boost/core/addressof.hpp:225:44: error: ‘operator&’ not defined  
  
I've uploaded the full output from boost build to [addressof-error.txt](https://github.com/boostorg/core/files/941661/addressof-error.txt). My command line was:  
  
    $ b2 gcc-4.7std11 -q > addressof-error.txt  
  
The relevant configuration from my `user-config.jam`:  
  
    using gcc : 4.7std11 : g++-4.7 --std=c++11 ;  
  
And gcc's version details:  
  
    $ g++-4.7 --version  
    g++-4.7 (Ubuntu/Linaro 4.7.4-3ubuntu12) 4.7.4  
    Copyright © 2012 Free Software Foundation, Inc.  
    This is free software; see the source for copying conditions.  There is NO  
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

---

## Comment 1

> Username: Lastique  
> Created at: 2017-04-20 10:10:11 UTC  
> Url: https://github.com/boostorg/core/issues/29#issuecomment-295665290  

I can't reproduce this in develop, master or 1.64 (from .tar.bz2 package) on Kubuntu 17.04, gcc:  
  
```  
g++-4.7 -v  
Using built-in specs.  
COLLECT_GCC=g++-4.7  
COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-linux-gnu/4.7/lto-wrapper  
Target: x86_64-linux-gnu  
Configured with: ../src/configure -v --with-pkgversion='Ubuntu/Linaro 4.7.4-3ubuntu12' --with-bugurl=file:///usr/share/doc/gcc-4.7/README.Bugs --enable-languages=c,c++,go,fortran,objc,obj-c++ --prefix=/usr --program-suffix=-4.7 --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --with-gxx-include-dir=/usr/include/c++/4.7 --libdir=/usr/lib --enable-nls --with-sysroot=/ --enable-clocale=gnu --enable-libstdcxx-debug --enable-gnu-unique-object --disable-libmudflap --enable-plugin --with-system-zlib --enable-objc-gc --with-cloog --enable-cloog-backend=ppl --disable-cloog-version-check --disable-ppl-version-check --enable-multiarch --disable-werror --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --with-tune=generic --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu  
Thread model: posix  
gcc version 4.7.4 (Ubuntu/Linaro 4.7.4-3ubuntu12)  
```  
  
Looks like the compiler version is the same.  
  
I remember we had similar errors on gcc 4.7 in an earlier version of `addressof`, but these were fixed before the code was merged to master. Are you sure you're using a clean and up to date source tree?

---

## Comment 2

> Username: Lastique  
> Created at: 2017-04-20 10:47:34 UTC  
> Url: https://github.com/boostorg/core/issues/29#issuecomment-295678961  

Please, verify that your `boost/config/compiler/gcc.hpp` contains `#define BOOST_NO_CXX11_SFINAE_EXPR` for gcc 4.7 (line 256 in 1.64). That macro, when defined, should disable `constexpr addressof`, which gives the error in your log.

---

## Comment 3

> Username: pdimov  
> Created at: 2017-04-20 10:51:35 UTC  
> Url: https://github.com/boostorg/core/issues/29#issuecomment-295680352  

We also have g++ 4.7 in Travis.

---

## Comment 4

> Username: glenfe  
> Created at: 2017-04-20 12:09:06 UTC  
> Updated at: 2017-04-20 12:09:32 UTC  
> Url: https://github.com/boostorg/core/issues/29#issuecomment-295710027  

I also tested with g++ 4.7 on Ubuntu 17.04 (`sudo apt install g++4.7`) which was 4.7.4, without any issues.

---

## Comment 5

> Username: danieljames  
> Created at: 2017-04-20 12:12:02 UTC  
> Url: https://github.com/boostorg/core/issues/29#issuecomment-295711232  

I'll check when I get home later. I'm pretty sure I tested with the tags 1.63.0 and 1.64.0 of core when I got this, but I didn't check config.

---

## Comment 6

> Username: Lastique  
> Created at: 2017-04-20 12:26:39 UTC  
> Url: https://github.com/boostorg/core/issues/29#issuecomment-295716995  

That piece of code relies on a new feature of Boost.Config, so make sure to update it as well.

---

## Comment 7

> Username: danieljames  
> Created at: 2017-04-20 16:19:40 UTC  
> Url: https://github.com/boostorg/core/issues/29#issuecomment-295798809  

Config was out of date.
