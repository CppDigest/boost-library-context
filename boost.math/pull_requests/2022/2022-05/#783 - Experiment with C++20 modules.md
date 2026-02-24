# #783 Experiment with C++20 modules. [Open]

> Username: jzmaddock  
> Created at: 2022-05-02 17:47:46 UTC  
> Updated at: 2025-10-23 07:40:12 UTC  
> Url: https://github.com/boostorg/math/pull/783  

OK folks, here's a bit of an experiment with C++ modules, it works with msvc but gcc leads to a fairly hopeless spiral of internal compiler errors :(  
  
There are 4 modules:  
  
core.  
constants  
special_functions  
  
and then an overarching "everything" module which just import/export's the others.  
  
I haven't tried with clang - mostly because it's command line use is problematic for modules and doesn't integrate with the build system well.  
  
msvc/gcc needed no modification to the build system to "just work", except that, module dependencies need to be explicitly specified to ensure there is a "builds before" relationship between module definition and user.  There's some considerable hot air expended on this subject on the web as it breaks parallel builds, and so may actually make the build time longer if you're not careful.  
  
Anyhow, even though this is a somewhat "failed experiment" at present, I'm submitting as a draft PR for discussion, and we'll see what happens with the next gcc release.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2022-05-03 00:38:03 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-1115501141  

@jzmaddock : How do you build it?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-05-03 07:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-1115815548  

> How do you build it?  
  
With gcc/msvc the supplied Jamfile will build things, but the special_functions module will fail to build with gcc (core and constants work OK though).  
  
Or in msvc IDE you can create a static lib project for the module files and set "build as module interface" under advanced settings.  
  
clang, I haven't figured out the command line yet.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-05-04 17:36:01 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-1117623019  

Update: I have managed to build the "core" module with clang-14, but not use it, I get:  
  
```  
In module 'boost.math.core' imported from ../../module/constants.cxx:28:  
D:/compilers/msys64/mingw64/include/c++/11.3.0/string_view:146:2: error: 'std::basic_string_view::basic_string_view<_CharT, _Traits>' from module 'boost.math.core.<global>' is not present in definition of 'basic_string_view<_CharT, _Traits>' provided earlier  
        basic_string_view(_It __first, _End __last)  
        ^  
D:/compilers/msys64/mingw64/include/c++/11.3.0/string_view:124:7: note: declaration of 'basic_string_view<_CharT, _Traits>' does not match  
      basic_string_view() noexcept  
      ^  
D:/compilers/msys64/mingw64/include/c++/11.3.0/string_view:128:17: note: declaration of 'basic_string_view<_CharT, _Traits>' does not match  
      constexpr basic_string_view(const basic_string_view&) noexcept = default;  
                ^  
D:/compilers/msys64/mingw64/include/c++/11.3.0/string_view:131:7: note: declaration of 'basic_string_view<_CharT, _Traits>' does not match  
      basic_string_view(const _CharT* __str) noexcept  
      ^  
D:/compilers/msys64/mingw64/include/c++/11.3.0/string_view:137:7: note: declaration of 'basic_string_view<_CharT, _Traits>' does not match  
      basic_string_view(const _CharT* __str, size_type __len) noexcept  
      ^  
D:/compilers/msys64/mingw64/include/c++/11.3.0/string_view:146:2: note: declaration of 'basic_string_view<_CharT, _Traits>' does not match  
        basic_string_view(_It __first, _End __last)  
```  
  
I see other folks around the web see similar errors, but with no solutions thus far :(

---

## Comment 4

> Username: NAThompson  
> Created_at: 2023-02-04 18:53:34 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-1416824947  

@jzmaddock : Has the module situation improved over the past year? I'm wondering if this PR might work now . .

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2023-02-04 19:35:31 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-1416833296  

Just tried it with GCC-12 and still see a slew of internal compiler errors.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2023-02-04 19:41:15 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-1416834317  

We'll try again in 2024!

---

## Comment 7

> Username: mborland  
> Created_at: 2023-10-20 05:39:31 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-1772114532  

Looks like we might finally be in business: https://www.kitware.com/import-cmake-the-experiment-is-over/

---

## Comment 8

> Username: mborland  
> Created_at: 2024-04-04 07:26:09 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-2036390031  

I pushed a couple of small updates to get this compiling under GCC-13. With C++20 and 23 you can now import std; so that version switch is in as well. I need to try under Clang-18 which came out last week and I think GCC-14 will be out in the next few weeks.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2024-04-07 11:29:32 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-2041437620  

This *almost* works with gcc-13 and clang-18.  
  
I'm going to try merging develop to this next, as I know that touches some of the problem areas anyway...

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2024-04-09 12:14:20 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-2045018535  

This now works (such as it is) with MSVC-17.9.5 and clang-18.  GCC-13 still ICE's.

---

## Comment 11

> Username: mborland  
> Created_at: 2024-04-09 15:22:02 UTC  
> Updated_at: 2024-04-09 15:22:13 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-2045466290  

> This now works (such as it is) with MSVC-17.9.5 and clang-18. GCC-13 still ICE's.  
  
Should we change the file extension of the module to `.ixx` (MSVC) or `.cppm` (other tutorials)? It looks like the current `.cxx` collides with one of the compilers are used so the module is now committed. We could then add `.gcm` (GCC), `.ifc` (MSVC), and `.cxx` (Presumably clang) to the gitignore.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2024-04-09 15:45:28 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-2045521425  

This sucks, as different compilers require different naming conventions :(  
  
I'm easy either way though.

---

## Comment 13

> Username: mborland  
> Created_at: 2024-04-09 15:51:37 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-2045535343  

It looks like Dani went with `.ixx` in her demo she mentioned on the ML: https://github.com/DanielaE/CppInAction. Since that's proven to work it seems like the safe choice.

---

## Comment 14

> Username: mborland  
> Created_at: 2024-04-18 06:20:22 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-2063085196  

Cmake 3.30 will give us experimental `import std;` support: https://gitlab.kitware.com/cmake/cmake/-/merge_requests/9337. Apparently it's already possible on MSVC but you have to go in and build it yourself and link with 17.10 preview. Since I'm generally unfamiliar with MSVC I did not make it very far.

---

## Comment 15

> Username: ChuanqiXu9  
> Created_at: 2025-10-23 06:48:02 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-3435413833  

not a ping and just out of curiosity, what's the status now?

---

## Comment 16

> Username: mborland  
> Created_at: 2025-10-23 07:40:12 UTC  
> Url: https://github.com/boostorg/math/pull/783#issuecomment-3435563432  

> not a ping and just out of curiosity, what's the status now?  
  
Truthfully, I haven't touched this since my last comments in April of last year. I doubt John has either. I know the situation is much better now. In a different lib I was actually able to get GCC modules to work with b2 for running the current test suite: https://github.com/cppalliance/decimal/blob/develop/modules/Jamfile

---
