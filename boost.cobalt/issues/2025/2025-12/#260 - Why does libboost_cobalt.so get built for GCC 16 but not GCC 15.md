# #260 - Why does libboost_cobalt.so get built for GCC 16 but not GCC 15? [Open]

> Username: jwakely  
> Created at: 2025-12-22 21:43:17 UTC  
> Updated at: 2026-01-05 12:31:03 UTC  
> Url: https://github.com/boostorg/cobalt/issues/260  

I'm packaging Boost 1.90.0 for Fedora and observed that when my new `boost-1.90.0` RPM is built using GCC 15 (the current compiler in Fedora Rawhide) there are no libs built for Boost.Cobalt. When I experiment with building it with the GCC 16 package that will arrive in Rawhide some time next month, I get these extra files installed:  
  
```  
   /usr/lib/debug/usr/lib64/libboost_cobalt.so.1.90.0-1.90.0-0.1.fc44.x86_64.debug  
   /usr/lib/debug/usr/lib64/libboost_cobalt_io.so.1.90.0-1.90.0-0.1.fc44.x86_64.debug  
   /usr/lib64/libboost_cobalt.so  
   /usr/lib64/libboost_cobalt.so.1.90.0  
   /usr/lib64/libboost_cobalt_io.so  
   /usr/lib64/libboost_cobalt_io.so.1.90.0  
```  
  
I assume this is because GCC 16 defaults to `-std=gnu++20` so supports C++20 coroutines by default, and GCC 15 only supports them if you use an extra option. But is that really the intended behaviour? Should building Boost automatically add `-std=gnu++20` when building Cobalt, so that it actually installs its libraries?  
  
https://www.boost.org/doc/libs/1_90_0/libs/cobalt/doc/html/index.html#requirements says it's been tested with GCC 10, but says nothing about it not being supported out of the box and requiring additional steps to get the library binaries. Am I doing something wrong when building Boost? I'm just using the `./bootstrap.sh` and `b2` commands as recommended.

---

## Comment 1

> Username: jwakely  
> Created at: 2025-12-22 22:09:10 UTC  
> Url: https://github.com/boostorg/cobalt/issues/260#issuecomment-3684343279  

I'm starting to suspect this is a bug, but I don't understand the boost build system well enough to say where the bug is.  
  
You can get binary packages of GCC 16 from https://jwakely.github.io/pkg-gcc-latest/ to experiment with.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2025-12-23 00:18:04 UTC  
> Url: https://github.com/boostorg/cobalt/issues/260#issuecomment-3684619016  

You need to set the standard like so:  
  
```shell  
./b2 cxxstd=20  
```  
  
The cobalt build scripts actually don't check the standard directly, but check if concepts compile - which is why gcc 16 works.  
  
That behaviour is intended, cobalt doesn't automatically change the C++ standard for the build.

---

## Comment 3

> Username: jwakely  
> Created at: 2026-01-05 11:39:12 UTC  
> Updated at: 2026-01-05 11:39:28 UTC  
> Url: https://github.com/boostorg/cobalt/issues/260#issuecomment-3710081168  

So it's expected that you need to link with `-lboost_cobalt` when using cobalt in C++20 code? But not when using it in C++17 code?  
  
So if you have a library which uses Boost.Cobalt and sometimes you use that library in C++17 code and sometimes in C++20 code, you need to have a separate installation of Boost libs which is built with C++20? That seems incredibly user hostile. Why doesn't the default boost build+install process (i.e. `bootstrap.sh` and `b2`) just build and install all the libs that are needed to use Boost.Cobalt?

---

## Comment 4

> Username: lrineau  
> Created at: 2026-01-05 11:51:12 UTC  
> Updated at: 2026-01-05 11:52:23 UTC  
> Url: https://github.com/boostorg/cobalt/issues/260#issuecomment-3710115931  

> So it's expected that you need to link with `-lboost_cobalt` when using cobalt in C++20 code? But not when using it in C++17 code?  
  
Boost Cobalt [requires C++20](https://www.boost.org/doc/libs/1_90_0/libs/cobalt/doc/html/index.html#requirements). If the current compiler does not implement C++20, then it is not built at all. With GCC 15, Boost Cobalt would require the compilation flag `-std=c++20`, but GCC 16 switched to C++20 by default.

---

## Comment 5

> Username: lrineau  
> Created at: 2026-01-05 11:52:54 UTC  
> Url: https://github.com/boostorg/cobalt/issues/260#issuecomment-3710121003  

> > So it's expected that you need to link with `-lboost_cobalt` when using cobalt in C++20 code? But not when using it in C++17 code?  
>   
> Boost Cobalt [requires C++20](https://www.boost.org/doc/libs/1_90_0/libs/cobalt/doc/html/index.html#requirements). If the current compiler does not implement C++20, then it is not built at all. With GCC 15, Boost Cobalt would require the compilation flag `-std=c++20`, but GCC 16 switched to C++20 by default.  
  
I mean: you cannot use Boost Cobalt in C++17 code.

---

## Comment 6

> Username: jwakely  
> Created at: 2026-01-05 11:55:00 UTC  
> Url: https://github.com/boostorg/cobalt/issues/260#issuecomment-3710127028  

So then wouldn't it make sense for the build to add `cxxstd=20` automatically (after checking if it's supported by the compiler) so that the default build+install process Just Works?  
  
This is the first boost library I'm aware of that can't be installed out of the box using the standard documented process for installing Boost from source.

---

## Comment 7

> Username: jwakely  
> Created at: 2026-01-05 11:58:25 UTC  
> Url: https://github.com/boostorg/cobalt/issues/260#issuecomment-3710137693  

> You need to set the standard like so:  
>   
> ./b2 cxxstd=20  
  
How can I add that just for building cobalt?  
  
For a distro still using GCC 15, should I do `b2 --without-cobalt ...` for all the other libs, and then a second step using `b2 --with-cobalt cxxstd=20` ? I want to ensure that `libboost_cobalt.so` and `libboost_cobalt_io.so` are built and packaged for users of the distro. If they need to use `-std=c++20` to use cobalt, that's fine, but the libs need to be present in the distro's packages for that to work.  
  
Are there side effects for building everything else in Boost with cxxstd=20?

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2026-01-05 12:31:03 UTC  
> Url: https://github.com/boostorg/cobalt/issues/260#issuecomment-3710237091  

I would hope the are ABI compatible, in which case either works. I would probably just compile with C++20 on GCC 15. The thing was released in 2025, they're just conservative with the default C++ standards.
