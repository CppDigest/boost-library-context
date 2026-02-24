# #609 Fix Beast include directories for cmake targets [Closed]

> Username: inetknght  
> Created at: 2017-07-06 17:21:00 UTC  
> Updated at: 2017-07-07 18:01:53 UTC  
> Closed at: 2017-07-07 17:01:35 UTC  
> Url: https://github.com/boostorg/beast/pull/609  

Using CMake, Beast does not seem to have an install target (`cmake beast && make -j$(nproc) && make install` will not install anything). That might be handy to add somewhere if you intend to keep it useable separate from Boost's bjam.  
  
I tried copying the example/server-framework to its own CMake project, added Beast as a git submodule, and asked cmake to `add_subdirectory(beast)`. Adding separate projects in this fashion is actually an anti-pattern, but it's a common one. It almost worked... there's a bug with Beast's include directory specification, resolving it is the purpose of this pull request.  
  
It appears that the Boost libraries' include directories were specified twice and the local include directory was not specified at all.  
  
```  
In file included from beast-test/src/http_async_port.hpp:13:0,  
                 from beast-test/src/main.cpp:10:  
beast-test/src/http_base.hpp:11:33: fatal error: beast/core/string.hpp: No such file or directory  
 #include <beast/core/string.hpp>  
                                 ^  
compilation terminated.  
```  
  
To highlight, I created https://github.com/inetknght/beast-test  
You can copy-paste from the README.md into a Linux environment (I'm using GCC 6.3, Fedora 25, cmake 3.7, and Boost 1.62)  
  
I am quite curious if there's already an existing repository with a better laid out example of how to use beast as a cmake dependency?

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-07-06 18:04:44 UTC  
> Updated_at: 2017-07-06 18:05:32 UTC  
> Url: https://github.com/boostorg/beast/pull/609#issuecomment-313474063  

# [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/609?src=pr&el=h1) Report  
> Merging [#609](https://codecov.io/gh/vinniefalco/Beast/pull/609?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/Beast/commit/89c416cde64de3265299ced216a6eef9428c2a51?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/Beast/pull/609/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/vinniefalco/Beast/pull/609?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #609   +/-   ##  
========================================  
  Coverage    93.84%   93.84%             
========================================  
  Files           94       94             
  Lines         7476     7476             
========================================  
  Hits          7016     7016             
  Misses         460      460  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/609?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/Beast/pull/609?src=pr&el=footer). Last update [89c416c...7bcefb0](https://codecov.io/gh/vinniefalco/Beast/pull/609?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-07-06 19:18:27 UTC  
> Url: https://github.com/boostorg/beast/pull/609#issuecomment-313493063  

So, this is the problem - Beast is being proposed for Boost and if accepted, will simply be provided and integrated into projects as any other Boost library. The CMake INTERFACE targets are superfluous. I did not write those targets, they were generously provided by other users. I don't use them, and I don't maintain them. And they will be removed with 100% certainty when Beast is merged into Boost. There are no tests for these targets either. As we know, *"That which is untested, is broken"*. This is no exception.  
  
I appreciate the thoughtfulness but I am reluctant to make further changes to it other than to remove it. The CMake file exists for people who want to build Visual Studio project files for the examples and tests (like me). The INTERFACE target is obsoleted with a Boost acceptance.

---

## Comment 3

> Username: inetknght  
> Created_at: 2017-07-06 21:32:26 UTC  
> Url: https://github.com/boostorg/beast/pull/609#issuecomment-313525392  

Hi @vinniefalco,  
  
Thanks for the quick heads up. I'm disappointed, but I fully understand it being considered for Boost. I definitely look forward to it _being_ part of Boost. Is there any timeframe of expected acceptance?  
  
You mentioned that you use CMake just to build project files for Visual Studio (or XCode, from reading the readme). I use CMake for more; Qt Creator and CLion are (can be) powered by CMake. I use it for the IDE integration, for the build system, and a little bit of dependency management (eg, _finding_ dependencies).  
  
The example files are pretty awesome. I think they really exemplify the use of Beast. But I think they don't well-demonstrate how to use Beast as a dependency; they depend on Beast, but are also _part_ of Beast's CMake project.  
  
I really like the server-framework example in particular. I want to build off of that to learn more about HTTP, WebSocket, and TLS-enabled servers. It specifically calls that out, [_the server is designed to use modular components that users may simply copy into their own project to get started quickly._](https://github.com/vinniefalco/Beast/blob/develop/example/server-framework/README.md).  
  
So that's what I'd tried to do and was disappointed to find it didn't quite work as I'd hoped.  
  
Is there an example repository that uses Beast as a dependency, with or without CMake?

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-07-06 22:31:35 UTC  
> Url: https://github.com/boostorg/beast/pull/609#issuecomment-313537065  

>Is there any timeframe of expected acceptance?  
  
The earliest possible version is Boost 1.66.0 which is December 2017  
  
>Is there an example repository that uses Beast as a dependency, with or without CMake?  
  
No. My thinking is, how hard is it to add one line `-I/path/to/beast/include`?  
  
This issue keeps coming up from different people so I guess it should be addressed. The problem is I don't know CMake, there's no tests for the INTERFACE target, and I can't evaluate these changes meaningfully. Still, I will try to merge it and see what happens. Keep in mind that someone else came before you, saying the same things, and gave us the INTERFACE target that we have now.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2017-07-06 22:37:46 UTC  
> Url: https://github.com/boostorg/beast/pull/609#issuecomment-313538218  

Oh hey  
>https://github.com/inetknght/beast-test  
  
I see what you did there :) That's quite nice. Its unfortunately got a shelf life but it could still provide 6 months of good times!

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2017-07-06 23:55:34 UTC  
> Url: https://github.com/boostorg/beast/pull/609#issuecomment-313550458  

It seems to build and looks harmless

---

## Comment 7

> Username: xsacha  
> Created_at: 2017-07-07 04:49:24 UTC  
> Updated_at: 2017-07-07 04:51:53 UTC  
> Url: https://github.com/boostorg/beast/pull/609#issuecomment-313586103  

Hi,  
The install part wasn't done at request of @vinniefalco .  
  
However, I have added the correct install interfaces in to Hunter project ( https://github.com/hunter-packages/Beast ). I only have b32 and b50 in there at this stage as it was quite stable. If you find a particularly stable version I can add it in fairly simply.  
  
If you use Hunter, it is simply a matter of:  
hunter_add_package(Beast)  
find_package(Beast)  
  
Then just link Beast to your executable and everything will be pulled in automatically, including Boost :)  
  
Otherwise, with your change here, it should be possibly to do add_subdirectory(Beast) out of the box.

---

## Comment 8

> Username: keith-bennett-gbg  
> Created_at: 2017-07-07 16:09:16 UTC  
> Url: https://github.com/boostorg/beast/pull/609#issuecomment-313725006  

> The earliest possible version is Boost 1.66.0 which is December 2017  
  
That would be great. I hope feedback & use will help to achieve that target.  
  
> No. My thinking is, how hard is it to add one line -I/path/to/beast/include?  
  
For a single developer, with a single dependency, it's not hard. For a multi-developer project, you then have to consider where that path might point to for everyone. And, if _every_ dependency worked like that, you'd have a giant mess of include paths to add and maintain. It gets worse then the project then becomes a dependency of other things.  
  
So that's one of the things that I think CMake tries to solve, usually to good effect. Let it figure out where things are (or give it hints). Then it will add those paths (include paths, library paths, etc) to the compilation units that need it, in the order that they're needed, using the specific compiler's method.  
  
> This issue keeps coming up from different people so I guess it should be addressed.  
  
Indeed.  
  
> The problem is I don't know CMake  
  
Nobody's perfect. I know I need to learn more about boost-build! :)  
  
If you'd like to learn basics of CMake, it's generally well [documented](https://cmake.org/documentation/) and includes a [tutorial](https://cmake.org/cmake-tutorial/). I recommend learning it since it's free, many C++ projects use it, and it's relatively easy to get small projects up and running. Plus, as you've already noted, it's handy for making project files for your favorite IDEs.  
  
I've found I learn things best by asking questions though. It's handy to have someone to whom I could bounce questions.  
  
> there's no tests for the INTERFACE target  
  
That can be addressed.  
  
> I can't evaluate these changes meaningfully.  
  
The wonderful thing about open source projects is that you can sometimes find high quality people who can and will evaluate the changes meaningfully.  
  
> Still, I will try to merge it and see what happens.  
  
That'd be cool.  
  
> Keep in mind that someone else came before you, saying the same things, and gave us the INTERFACE target that we have now.  
  
I'd be curious to see how their project uses Beast as a build dependency.  
  
> I see what you did there :) That's quite nice. Its unfortunately got a shelf life but it could still provide 6 months of good times!  
  
Shelf life could be longer than that; when Beast gets included to Boost, then [that repository](https://github.com/inetknght/beast-test) can be updated to depend on the Boost version instead of (or perhaps in addition to) the current standalone version.

---

## Comment 9

> Username: keith-bennett-gbg  
> Created_at: 2017-07-07 16:11:31 UTC  
> Url: https://github.com/boostorg/beast/pull/609#issuecomment-313725578  

> I have added the correct install interfaces in to Hunter project ( https://github.com/hunter-packages/Beast ). I only have b32 and b50 in there at this stage as it was quite stable. If you find a particularly stable version I can add it in fairly simply.  
>  
> If you use Hunter, it is simply a matter of:  
> hunter_add_package(Beast)  
> find_package(Beast)  
>  
> Then just link Beast to your executable and everything will be pulled in automatically, including Boost :)  
  
I'll have to take a look at Hunter and see what it's about. Thanks for the heads up! Honestly I'd prefer to use bleeding edge versions though, particularly until I find out what I would want to use that is or is not available in previous versions. I think that's a good way of helping to _find_ problems.  
  
> Otherwise, with your change here, it should be possibly to do add_subdirectory(Beast) out of the box.  
  
:)

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2017-07-07 16:40:40 UTC  
> Url: https://github.com/boostorg/beast/pull/609#issuecomment-313732599  

If you want to help maintain this script I will of course accept any pull requests that pass tests :)  
  
I recognize that this use case is decidedly popular so it would be a welcome addition

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2017-07-07 16:50:07 UTC  
> Url: https://github.com/boostorg/beast/pull/609#issuecomment-313734925  

Just wondering, who hasn't starred the repo? ;)

---

## Comment 12

> Username: vinniefalco  
> Created_at: 2017-07-07 17:01:35 UTC  
> Url: https://github.com/boostorg/beast/pull/609#issuecomment-313737858  

Did you change the commit after I put it in **v74**? Please verify its the same commit:  
https://github.com/vinniefalco/Beast/pull/613  
  
If different let me know. For now I am closing this, it will be merged in v74.

---

## Comment 13

> Username: inetknght  
> Created_at: 2017-07-07 18:01:53 UTC  
> Url: https://github.com/boostorg/beast/pull/609#issuecomment-313752283  

I did not change the commits in this #609 after merged into v74. I see the commit in #613.  
  
Thanks for the merge.

---
