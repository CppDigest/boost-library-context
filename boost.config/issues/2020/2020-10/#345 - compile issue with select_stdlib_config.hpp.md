# #345 - compile issue with select_stdlib_config.hpp [Closed]

> Username: ajkerman  
> Created at: 2020-10-13 15:17:50 UTC  
> Updated at: 2021-08-15 18:19:34 UTC  
> Closed at: 2021-08-15 18:19:34 UTC  
> Url: https://github.com/boostorg/config/issues/345  

The newer version of the header file "select_stdlib_config.hpp" which appeared starting with v1.73 causes compile errors when there is another library in the include path that contains a file named "version". It appears that in this case, the __has_include() macro finds such a file and then the header erroneously tried to include it.  
  
In my case, i am using the ODB C++ API for relational databases which has a file named "version". In order to allow Boost to compile, i need to modify its "select_stdlib_config.hpp" header file to remove the #if defined(__cplusplus) && defined(__has_include) conditional and replace it with the older code which went straight to #ifdef __cplusplus, etc..

---

## Comment 1

> Username: TeMPOraL  
> Created at: 2021-02-23 16:56:14 UTC  
> Updated at: 2021-02-23 16:58:56 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-784348229  

Also just hit the same issue when upgrading to 1.73 - `select_stdlib_config.hpp` picks up on the file named `VERSION`, which is in include path of nanodbc library.  
  
I'm also aware of another library which ended up renaming their "version" file because of this problem: https://github.com/FlexibleSUSY/FlexibleSUSY/issues/267.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-02-23 18:30:28 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-784417264  

I hear you, but look, <version> is a C++ standard library header name now, we really do need to be able to #include it, and not just here, and not just in Boost.

---

## Comment 3

> Username: TeMPOraL  
> Created at: 2021-02-23 19:25:52 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-784451953  

Huh, fair enough. I didn't think through the implications of `<version>` being a standard header now.

---

## Comment 4

> Username: dmenendez-gruposantander  
> Created at: 2021-03-08 16:30:25 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-792879320  

Same problem here, upgrading to 1.75  
  
> we really do need to be able to #include it  
  
To me the problem is that you would like to `#include <version>` only when you *know* that it is the standard library header file, but not if it's something else.  
If your standard library does not have that header, then you cannot just rely on __has_include() because that will give you the wrong answer. That's why I think this is a bug in Boost.Config.  
  
Maybe there should be a __has_std_include() to detect the presence of an actual standard library header file with a given name, which is a different answer to detecting the presence of any file with a given name in the include search path.  
  
I have several third part libraries that have a 'version' file right next to its 'include' directory, and require it to be in the header search path. Boost.Config is forcing me to rename all those 'version' files, and I don't think that it should.  
  
Could there be an out-out macro, or any other way to avoid renaming files in my third-party libraries?

---

## Comment 5

> Username: Flamefire  
> Created at: 2021-04-09 10:35:20 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-816590519  

So basically the issue occurs when a project or one of its dependencies adds a folder to the include paths explicitly (with `-I`) which contains a file called `version` (with no extension)  
  
The point is: All of those projects are invalid as of C++20 as (AFAIK) replacing a standard header is UB (undefined behavior) and that is what those projects are doing.  
  
There really is no solution outside of making those projects NOT put a file "version" into their include paths because a `-I` option takes precedence over the system include paths.  
  
> any other way to avoid renaming files in my third-party libraries?  
  
The only answer here is: File an issue with the autor(s) to fix this.  
  
In general I'd very much encourage project authors to provide proper namespacing of their files:  
  
- Don't add the root folder to `-I`, but use a subfolder (commonly: `include`, or when combining headers and sources: `src`)  
- Put all "your" files into named subfolders and include as `#include <yourlib/yourheader.hpp>` to avoid name clashes with dependencies or consumers  
  
So TLDR: Either don't use `version` as a file name for your version, but put it in the header, Readme, ... or make sure it doesn't end up in the include path(s) by putting it somewhere else in the file hierarchy  
This is **not** a boost issue, but an issue with the libraries used.  
  
See also https://en.cppreference.com/w/cpp/header/version and https://en.cppreference.com/w/cpp/feature_test#Example_2 which even has an example that uses the exact same way as Boost does.

---

## Comment 6

> Username: Mike-Devel  
> Created at: 2021-04-09 22:34:18 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-817008037  

> So basically the issue occurs when a project or one of its dependencies adds a folder to the include paths explicitly (with -I) which contains a file called version (with no extension)  
  
I'm not quite sure, but in practice, it  might only be a problem, if those projects are compiled with a standard library that doesn't provide the `version` header yet itself. Because if it does, I'd expect that it will be found before the one "provided" by the project.  
  
> Don't add the root folder to -I  
  
Tell that to boost ;)

---

## Comment 7

> Username: Flamefire  
> Created at: 2021-04-10 08:46:53 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-817102505  

>  Because if it does, I'd expect that it will be found before the one "provided" by the project.  
  
No. I verified that. The system headers are not special. Any header found via `-I` is used first. The system folder(s) are searched last.  
  
> Tell that to boost ;)  
  
Not a fan of that and at least the sublibs follow the proper procedure. I don't see why not just make a top-level "include" folder by `b2 headers` instead

---

## Comment 8

> Username: mclow  
> Created at: 2021-04-10 22:24:24 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-817210290  

> > So basically the issue occurs when a project or one of its dependencies adds a folder to the include paths explicitly (with -I) which contains a file called version (with no extension)  
  
Yes.  
  
[snip]  
> > Don't add the root folder to -I  
>   
> Tell that to boost ;)  
  
Boost does not contain a file named `version`

---

## Comment 9

> Username: Mike-Devel  
> Created at: 2021-04-10 22:50:51 UTC  
> Updated at: 2021-04-10 22:51:13 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-817212581  

> Boost does not contain a file named version  
  
No, and I didn't say it does.

---

## Comment 10

> Username: dmenendez-gruposantander  
> Created at: 2021-04-12 08:00:38 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-817582826  

> The point is: All of those projects are invalid as of C++20   
  
Totally agree!  
But Boost is doing `#include <version>` without checking if it is compiling in C++20!  
  
I'm compiling in C++14 and I don't think `#include <version>` should be "special" in the way Boost.Config thinks it is.  
That's why I claim this is a bug in Boost.Config, which is guarding this `#include <version>` with `__has_include()` and not with both `__has_include()` **and** (the spiritual equivalent to) `#ifdef C++20`

---

## Comment 11

> Username: jzmaddock  
> Created at: 2021-04-12 09:10:55 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-817635223  

> But Boost is doing #include <version> without checking if it is compiling in C++20!  
  
Strictly speaking, you are course correct.  However, the header is perfectly usable in non-C++20 mode, and contains useful information (from Boost.Config's POV) about features from C++11 onwards, it is also about the most lightweight std lib header we can use to identify which std lib we have.  Likewise __has_include is actually a C++17 feature, but is usable in all C++ versions in compilers which support it (C++98 if necessary!).

---

## Comment 12

> Username: dmenendez-gruposantander  
> Created at: 2021-04-12 11:16:19 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-817725104  

> > But Boost is doing #include  without checking if it is compiling in C++20!  
>   
> the header is perfectly usable in non-C++20 mode  
  
Sorry but I disagree.  
  
Boost.Config is assuming that `#include <version>` will *always* get the C++20 standard header, and my point is that **before C++20** that is an incorrect assumption.  
  
And making that incorrect assumption is a bug which is breaking my build.

---

## Comment 13

> Username: dmenendez-gruposantander  
> Created at: 2021-04-12 11:30:20 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-817733532  

Let me try to be helpful: would you consider an opt-out user-definable preprocessor macro?  
Something that signals: `<version>` is not the ~~droids~~ standard header you are looking for  
like BOOST_CONFIG_NO_STD_VERSION or similar

---

## Comment 14

> Username: Flamefire  
> Created at: 2021-04-12 11:35:13 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-817736246  

Given that what we do here is 1:1 what the example code on https://en.cppreference.com/w/cpp/feature_test#Example_2 is, which explicitly is supposed to work for C++11 and up, I'd not change that in Boost.  
  
Again: This header exists also pre-C++20 (call it an extension), is usable and useful where it exists.

---

## Comment 15

> Username: jzmaddock  
> Created at: 2021-04-12 18:53:10 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-818049771  

>Boost.Config is assuming that #include <version> will always get the C++20 standard header, and my point is that before C++20 that is an incorrect assumption.  
  
On a purely practical note, take a look at the actual implementations of `<version>`, all three main vendors (gcc, clang/libcxx, and MSVC) tailor their `<version>` implementations to not only be usable, but genuinely useful prior to C++20.  Down the road, it rather looks to me like `<version>` will supersede Boost.Config altogether.

---

## Comment 16

> Username: dmenendez-gruposantander  
> Created at: 2021-04-13 07:25:23 UTC  
> Url: https://github.com/boostorg/config/issues/345#issuecomment-818508142  

Hum... if `<version>` as a standard header is going to win anyway, I might as well nag each project to rename or relocate their "version" file, as this will eventually blow up unrelated to Boost.  
  
Thanks for your time and insight on this issue.
