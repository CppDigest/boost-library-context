# #4 - building auto_index on macOS [Open]

> Username: sdarwin  
> Created at: 2023-09-14 00:47:46 UTC  
> Updated at: 2023-09-17 00:44:32 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4  

In release-tools/build_docs, I am testing building the boost documentation for all libraries on Windows, Linux, MacOS.  
  
The following libraries have a dependency on auto_index:  math multiprecision pool type_traits vmd  
  
I might be doing something wrong, but it seems that auto_index is not building on MacOS.   
  
```  
./b2 -q -d0 --build-dir=build --distdir=build/dist tools/quickbook tools/auto_index/build  
```  
  
The build completes without error and generates `quickbook`, but doesn't create the required file `build/dist/bin/auto_index`  
  
- Could GitHub Actions CI tests be added to auto_index including macos-11,12,13 ?  
- In the tests, check if the file build/dist/bin/auto_index is generated.  
  
@pdimov may have been working on adding tests, the most recent commit:  "attempt to get test dependencies to work".

---

## Comment 1

> Username: pdimov  
> Created at: 2023-09-14 01:22:47 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1718532821  

What's the output of `b2 --build-dir=build --distdir=build/dist tools/auto_index/build`?

---

## Comment 2

> Username: pdimov  
> Created at: 2023-09-14 01:44:10 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1718618095  

I suspect the reason is that macOS defaults to C++03 and we no longer support it. You'd have to pass `cxxstd=11` or higher.

---

## Comment 3

> Username: Lastique  
> Created at: 2023-09-14 07:56:43 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1718949433  

I think, Boost.Build should at least produce a warning if an explicitly specified target cannot be built with the given set of features (`cxxstd` in particular).

---

## Comment 4

> Username: sdarwin  
> Created at: 2023-09-14 13:07:43 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1719419804  

> What's the output of b2 --build-dir=build --distdir=build/dist tools/auto_index/build?  
  
```  
+ ./b2 -q -d3 --build-dir=build --distdir=build/dist tools/auto_index/build  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - symlinks supported       : yes (cached)  
    - has_icu builds           : no [2]  
    - has std::atomic_ref      : no [2]  
    - has statx                : no [2]  
    - has statx syscall        : no [2]  
    - has init_priority attribute : yes [2]  
    - has stat::st_blksize     : no [2]  
    - has stat::st_mtim        : no [2]  
    - has stat::st_mtimensec   : no [2]  
    - has stat::st_mtimespec   : yes [2]  
    - has stat::st_birthtim    : no [2]  
    - has stat::st_birthtimensec : no [2]  
    - has stat::st_birthtimespec : yes [2]  
    - has fdopendir(O_NOFOLLOW) : yes [2]  
    - has dirent::d_type       : yes [2]  
    - has POSIX *at APIs       : no [2]  
    - compiler supports SSE2   : yes [2]  
    - compiler supports SSE4.1 : yes [2]  
    - cxx11_static_assert      : no [2]  
    - compiler supports SSE2   : yes [3]  
    - compiler supports SSE4.1 : yes [3]  
    - cxx11_static_assert      : no [3]  
  
[1] clang-14  
[2] clang-darwin-14/release/link-static/python-2.7/threading-multi/visibility-hidden  
[3] clang-darwin-14/release/build-no/link-static/python-2.7/threading-multi/visibility-hidden  
make	--	all  
make	--	all  
time	--	all: unbound  
made	stable	all  
...found 1 target...  
total 49544  
+ ls -al build/dist/bin  
drwxr-xr-x  3 runner  staff       96 Sep 13 23:22 .  
drwxr-xr-x  3 runner  staff       96 Sep 13 23:21 ..  
-rwxr-xr-x  1 runner  staff  2646256 Sep 13 23:22 quickbook  
```  
  
> I think, Boost.Build should at least produce a warning   
  
Didn't seem to output a clear warning.  
  
> You'd have to pass cxxstd=11 or higher.  
  
Yes! I believe that fixed it. Continuing testing.  
  
- If this is a widespread issue affecting many boost libraries, perhaps b2 should be modified to set a default cxxstd=11 on MacOS?  
  
- If it's limited to auto_index, could the auto_index Jamfile detect when running on MacOS and set cxxstd=11.

---

## Comment 5

> Username: Lastique  
> Created at: 2023-09-14 14:58:18 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1719623344  

> > I think, Boost.Build should at least produce a warning  
>   
> Didn't seem to output a clear warning.  
  
I didn't mean "should" as in "I expect it to do so" but rather as "it should probably be enhanced to do so".

---

## Comment 6

> Username: sdarwin  
> Created at: 2023-09-14 15:04:36 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1719634745  

> should probably be enhanced  
  
Ah, ok!

---

## Comment 7

> Username: pdimov  
> Created at: 2023-09-14 16:49:12 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1719801561  

Boost.JSON defines a custom `requires` rule that produces a warning when `--with-json` is passed, but the library isn't built due to C++11 features not being available.  
  
https://github.com/boostorg/json/blob/a07e38be41813d698091e7abaa3bea76dc945c63/build/Jamfile  
  
Today we probably want this warning to be unconditional; in our case here we aren't passing `--with-atomic`.

---

## Comment 8

> Username: pdimov  
> Created at: 2023-09-14 16:52:24 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1719805918  

> If this is a widespread issue affecting many boost libraries, perhaps b2 should be modified to set a default cxxstd=11 on MacOS?  
  
It will be a widespread issue and will eventually (if not this release, then the next) affect virtually all Boost libraries and tools.  
  
We don't want "on macOS" but we probably do want "on toolset `clang-darwin`". Not sure if b2 will do that though because it's an independent project now and not specific to Boost.  
  
Maybe we could do that in the Boost Jamroot instead, somehow. I'm not sure how. @grafikrobot or @grisumbras might know.

---

## Comment 9

> Username: Lastique  
> Created at: 2023-09-15 08:54:30 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1720918867  

> Today we probably want this warning to be unconditional; in our case here we aren't passing `--with-atomic`.  
  
But `tools/auto_index/build` depends on Boost.Atomic and hence inherits the requirement of C++11. So the warning should be displayed, and should mention both `tools/auto_index/build` and `libs/atomic/build`.  
  
OTOH, if the user doesn't specify any `--with-*` flags or any targets, he should see no warnings as no targets were explicitly specified.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2023-09-15 16:28:35 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1721548408  

I'm away on holiday at present, but I will look at this at some point...

---

## Comment 11

> Username: jzmaddock  
> Created at: 2023-09-16 16:50:20 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1722269150  

I suspect that someone with access to the problem toolset will need to investigate some more.... using @pdimov 's command line, if I explicitly build with cxxstd=03 then I see copious build errors of the form:  
  
```  
/usr/lib/gcc/x86_64-w64-mingw32/11/include/c++/bits/c++0x_warning.h:32:2: error: #error This file requires compiler and library support for the ISO C++ 2011 standard. This support must be enabled with the -std=c++11 or -std=gnu++11 compiler options.  
```  
  
The dependency is auto_index->program options->any->type index-> <type_traits>  
  
I don't see a dependency to Atomic, but that's not to say it's not there.  In any case I see lots of breakages like this down the road...  
  
Ideally, what we need from Boost.Build is a way to say "this needs at least C++11" which is *not* the same as making `<cxxstd>11` a requirement.

---

## Comment 12

> Username: pdimov  
> Created at: 2023-09-16 16:58:23 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1722270642  

The dependency is auto_index -> filesystem -> atomic.  
  
https://github.com/boostorg/auto_index/blob/55bce3c4174e4130404b14a86db74ea6dd9ec874/build/Jamfile.v2#L27  
https://github.com/boostorg/filesystem/blob/fe07038a2d245178630eb38d255b555d6d14966e/build/Jamfile.v2#L92  
  
We do have a way to say "this needs at least C++11"  
  
https://github.com/boostorg/atomic/blob/ba4dc4c595a2b0e3913a9e3fecb2c0ad791eb0c9/build/Jamfile.v2#L46-L48  
https://github.com/boostorg/atomic/blob/ba4dc4c595a2b0e3913a9e3fecb2c0ad791eb0c9/build/Jamfile.v2#L148  
  
but the problem is that the result is <build>no in C++03 mode, which leads to the issue described here, where silently nothing gets built.

---

## Comment 13

> Username: jzmaddock  
> Created at: 2023-09-16 17:25:28 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1722276465  

> We do have a way to say "this needs at least C++11"  
  
Yes I know, I wrote that rule for Boost.Config, what I meant to say was a way for the std level to be set to at least C++11 if nothing is otherwise specified on the b2 command line.

---

## Comment 14

> Username: pdimov  
> Created at: 2023-09-16 17:36:44 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1722278939  

That's not easy because we don't in general know the default level when nothing is specified, but yes, it's doable in principle.  
  
In practice as I said above, it would probably be enough to make the default 11 (or better yet 14) specifically for clang-darwin.

---

## Comment 15

> Username: pdimov  
> Created at: 2023-09-16 17:38:48 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1722279424  

(In theory this is `default-build <toolset>clang-darwin:<cxxstd>14` but in practice `default-build` doesn't accept conditional requirements, so it's more convoluted.)

---

## Comment 16

> Username: Lastique  
> Created at: 2023-09-16 18:20:39 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1722286951  

Does it support `default-build <conditional>@rule`?  
  
I don't think we should check `toolset` property as (a) the same problem would apply to any compiler that defaults to C++03 and (b) toolset names can be redefined by the user. Ideally we want to compile a test program that checks `__cplusplus` value or something like that and make a decision based on that.

---

## Comment 17

> Username: grafikrobot  
> Created at: 2023-09-16 18:30:49 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1722288822  

> Ideally we want to compile a test program that checks `__cplusplus` value or something like that and make a decision based on that.  
  
That's why the Predef checks where invented. https://www.boost.org/doc/libs/1_83_0/libs/predef/doc/index.html#_using_with_boost_build

---

## Comment 18

> Username: Lastique  
> Created at: 2023-09-16 20:00:58 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1722305021  

That mechanism only supports macros defined by Boost.Predef, and to my knowledge Boost.Predef doesn't have a C++ version macro, does it? It doesn't look conceptually different from what Boost.Config provides.

---

## Comment 19

> Username: grafikrobot  
> Created at: 2023-09-16 20:06:25 UTC  
> Updated at: 2023-09-16 20:08:10 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1722306030  

> That mechanism only supports macros defined by Boost.Predef, and to my knowledge Boost.Predef doesn't have a C++ version macro, does it? It doesn't look conceptually different from what Boost.Config provides.  
  
https://www.boost.org/doc/libs/1_83_0/libs/predef/doc/index.html#_boost_lang_stdcpp  
  
AFAIK Config doesn't provide for a way to test the C++ version.  
  
PS. Config does provide testing specific C++ features. Which if that's what you want you should definitely use it.

---

## Comment 20

> Username: pdimov  
> Created at: 2023-09-16 22:30:39 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1722330623  

Even if we wanted to depend on Predef everywhere, `BOOST_LANG_STDCPP` doesn't work at the moment because it's 27 for MSVC, which doesn't set `__cplusplus` properly.  
  
It would be much better if we could just set `<mincxxstd>11` in the requirements, and get b2 to handle it, as its toolsets know what's the default `<cxxstd>` and what are its supported values.

---

## Comment 21

> Username: Lastique  
> Created at: 2023-09-16 23:53:12 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1722343940  

> AFAIK Config doesn't provide for a way to test the C++ version.  
  
It provides `BOOST_CXX_VERSION`, which gives the correct answer for MSVC too, but there isn't a dedicated check target for it. It should be easy to add it, if we want.  
  
> ...and get b2 to handle it, as its toolsets know what's the default `<cxxstd>`...  
  
Do they? I think they only know enough to support `<cxxstd>latest` but not the default. I'm looking specifically at clang and gcc.

---

## Comment 22

> Username: pdimov  
> Created at: 2023-09-17 00:44:32 UTC  
> Url: https://github.com/boostorg/auto_index/issues/4#issuecomment-1722354907  

Yes, you're right, they don't currently "know" but it will be fairly trivial to make them know because determining `latest` already requires (more) version-specific checks.
