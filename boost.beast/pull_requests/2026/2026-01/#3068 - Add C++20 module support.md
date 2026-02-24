# #3068 Add C++20 module support [Open]

> Username: mikomikotaishi  
> Created at: 2026-01-16 05:11:34 UTC  
> Updated at: 2026-02-09 09:59:00 UTC  
> Url: https://github.com/boostorg/beast/pull/3068  

This PR adds support for C++20 modules through CMake, as `boost.beast`. The exported classes are those listed in the [reference documentation](https://www.boost.org/doc/libs/latest/libs/beast/doc/html/beast/quickref.html).  
  
This is hopefully in line with other Boost libraries which have begun offering modules:  
* [`boost.any`](https://www.boost.org/libs/any)  
* [`boost.pfr`](https://www.boost.org/libs/pfr)  
* [`boost.regex`](https://www.boost.org/libs/regex)  
* [`boost.type_index`](https://www.boost.org/libs/type_index)

---

## Comment 1

> Username: mikomikotaishi  
> Created_at: 2026-01-16 05:14:28 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3758202782  

@vinniefalco Seeing as you are the last Boost organisation member to interact with the library that I can see, may I request a review from you?

---

## Comment 2

> Username: mikomikotaishi  
> Created_at: 2026-01-16 08:14:43 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3758697026  

I'm not sure why the Windows builds are failing, seeing as I haven't touched anything to do with the code itself. I don't expect it to fail, especially when the others do pass - I mostly notice the problem is with a missing file, that I didn't touch?

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2026-01-16 11:16:02 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3759557315  

I am not really qualified to review this but I will find someone who is ! Thank you

---

## Comment 4

> Username: mikomikotaishi  
> Created_at: 2026-01-28 19:51:59 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3813545101  

@vinniefalco Hi, any update on anyone who could review this PR?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2026-01-29 00:56:26 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3814739275  

@ashtum ?

---

## Comment 6

> Username: ashtum  
> Created_at: 2026-01-30 10:24:04 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3822978509  

Sorry for the late reply, and thank you @mikomikotaishi for your PR.  
  
There is ongoing research into making Boost consumable as C++20 modules, led by Rubén Pérez. The latest updates are available in his blog post: https://anarthal.github.io/cppblog/modules3  
  
Your work does not appear to be based on that effort, and if I’m not mistaken, it technically couldn’t be, since it requires modularizing libraries in dependency order. Given that, are you proposing this PR as a temporary solution? If so, as a Beast user, what specific problem does this solve for you? Is this something that could instead be done at the application level as a workaround?

---

## Comment 7

> Username: mikomikotaishi  
> Created_at: 2026-01-30 10:41:35 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3823050508  

Hi,  
  
While I haven't been familiar with that blog post prior nor did I explicitly copy its formula to build this PR, I do think that the contents of my PR resemble the following module interface that was described:  
```cpp  
  
  
// File: boost_mp11.cppm  
module; // Global module fragment  
#define BOOST_MP11_INTERFACE_UNIT // We want headers to actually declare entities  
                                  // No BOOST_CONFIG_SKIP_IMPORT_STD: import std is fine in the global module fragment  
#include <cassert>        // Some standard library headers need to be included for their macros  
#include <boost/mp11.hpp> // All entities are attached to the global module.  
  
export module boost.mp11;  
  
// List all symbols we want to export  
export namespace boost::mp11 {  
using mp11::list;  
}  
```  
This technique has pros and cons, and these can be ironed out as more users adopt or give feedback on the feature. Nonetheless, when you say "as a temporary solution", I think that this does provide some structure or means to use the library as a module, and this can be developed or made to conform more with the blog post's design goals, so I'd be happy to see this provided for the time being.  
  
A lot of my projects have been using modules as opposed to headers, and I find that once build system configuring and tooling is correctly configured it is nicer to use than traditional headers. While many things could be done at the application level (I've done this a lot, for example with asio/boost::asio, nlohmann::json, and other libraries, etc.), this feature does seem to have interest from others, such as building a better environment for module adoption where popular libraries can be easily supported with modules, and thus by just providing it with first-class support we would be able to make a lot of lives easier.

---

## Comment 8

> Username: ashtum  
> Created_at: 2026-01-30 13:37:10 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3823800140  

> I do think that the contents of my PR resemble the following module interface that was described:  
  
Is this part addressed in your PR:  
  
> We first need to make sure that our headers don’t include any third-party code when BOOST_USE_MODULES is defined. Standard library headers can be replaced by the equivalent Boost.Config compatibility headers. Boost dependencies don’t need to be updated. Some other headers may need to be ifdef’ed-out and included in the global module fragment.  
  
-----------------------  
  
> A lot of my projects have been using modules as opposed to headers, and I find that once build system configuring and tooling is correctly configured it is nicer to use than traditional headers. While many things could be done at the application level (I've done this a lot, for example with asio/boost::asio, nlohmann::json, and other libraries, etc.), this feature does seem to have interest from others, such as building a better environment for module adoption where popular libraries can be easily supported with modules, and thus by just providing it with first-class support we would be able to make a lot of lives easier.  
  
I believe the modularization was postponed due to a lack of proper tooling, but it may pick up again in the next release cycle. For now, a workaround on your side while waiting for the final solution might be the best option. @anarthal, any thoughts?

---

## Comment 9

> Username: mikomikotaishi  
> Created_at: 2026-01-30 16:07:17 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3824487705  

> We first need to make sure that our headers don’t include any third-party code when BOOST_USE_MODULES is defined. Standard library headers can be replaced by the equivalent Boost.Config compatibility headers. Boost dependencies don’t need to be updated. Some other headers may need to be ifdef’ed-out and included in the global module fragment.  
  
I don't think what this is prescribing against applies here, because the concern seems to be with accidentally exporting non-Boost symbols from the module. This isn't a problem because we manually exported each symbol. Of course, we can change this later in the future.  
  
> For now, a workaround on your side while waiting for the final solution might be the best option.  
  
Would it be unreasonable to ask that this could be merged into a separate branch for the time being?

---

## Comment 10

> Username: anarthal  
> Created_at: 2026-01-30 16:52:07 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3824698465  

Yup, jumping in into this discussion now. I wrote the linked blog post some time ago. Some important things I see here:  
  
**Running tests**  
   
My advice would be to run a decent amount of the test suite using the module before merging it. At least, I'd target the tests that exercise the public API. I don't think it's required by now to run those testing implementation details. The reason why I suggest this is because `export using` works, until it doesn't. For instance, exporting `using boost::beast::websocket::condition;` will very much likely run into the compiler discarding the `boost::system::is_error_code_enum` specialization (it's included in the GMF but it's probably not decl-reachable and subject to being discarded).  
  
Running tests requires you to:  
* Conditionally include or import the library in the tests, based on the macro you're defining. You might either directly use an ifdef, or place an ifdef in public headers so that they become imports when the macro is defined.  
* Set up CI (I know it's painful :))  
  
**import std**  
  
My personal view is that `import std` is important because it's the main source of compile-time gains. I'd personally not do modules without `import std`, but I agree that other people might think different, depending on the use-case. Supporting `import std` is currently very messy, so I wouldn't ask this PR to handle it.  
  
TL;DR: I think there are 3 options here:  
  
1. Merge this as-is, state that it's highly experimental and that things might break, on the basis that it might be useful to some users. I wouldn't feel very comfortable doing it because I would deem this as untested code, which I don't like (I'm probably too compulsive with tests). Up to the library maintainer.  
2. Request changes on this PR, asking to run the test suite and CI. This is doable but it's a pretty decent amount of work. I would understand if the PR author doesn't want/can't dedicate the required time. In any case, this would still be experimental, but we would be more confident.  
3. Wait until we modularize things bottom-up. This might be too conservative, since we don't know when this is going to happen.  
  
IMO 2 sounds the most reasonable to me, with 1 being doable if the maintainers like risk.  
  
Let me know if you go with 2 and need to get things reviewed/need assistance with CI.

---

## Comment 11

> Username: mikomikotaishi  
> Created_at: 2026-01-30 18:13:52 UTC  
> Updated_at: 2026-01-30 18:15:02 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3825019491  

I wouldn't mind working towards number 2, but I'm not very skilled with CI manipulation and have worked with it very seldom.  
  
import std is very difficult to actually get working, so much so that I have my own "fake" std module just because I have never managed to get the official one to work on CMake.

---

## Comment 12

> Username: anarthal  
> Created_at: 2026-01-30 22:59:49 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3826254217  

I feel you with `import std`. I don't know the exact internals of Beast CIs, but you might get some inspiration from the ones I'm writing for mp11 (I don't have gcc working yet though): https://github.com/anarthal/mp11/blob/feature/cxx20-modules/.github/workflows/ci.yml

---

## Comment 13

> Username: mikomikotaishi  
> Created_at: 2026-01-31 00:03:44 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3826578343  

In my experience Clang has much better support than GCC, so I'll probably try and use Clang for CI

---

## Comment 14

> Username: mikomikotaishi  
> Created_at: 2026-02-02 14:53:16 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3835607124  

@vinniefalco Is option 2 reasonable with you, that we work on setting up CI tests for this PR and then merge?

---

## Comment 15

> Username: vinniefalco  
> Created_at: 2026-02-02 18:02:10 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3836807138  

its up to Mohammad

---

## Comment 16

> Username: mikomikotaishi  
> Created_at: 2026-02-02 18:09:23 UTC  
> Updated_at: 2026-02-02 18:09:39 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3836839137  

cc. @ashtum

---

## Comment 17

> Username: ashtum  
> Created_at: 2026-02-06 10:00:25 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3859283563  

> cc. @ashtum  
  
How are you currently consuming Beast as a dependency? Are you cloning the superproject with submodules, or downloading a Boost CMake release from https://github.com/boostorg/boost/releases? As far as I know, the official releases and installed versions don’t yet include proper CMake support for consuming the libraries as C++20 modules.  
  
Since any workaround would be temporary, I’d recommend applying these changes in your own branch for now, until the libraries are modularized in dependency order.

---

## Comment 18

> Username: mikomikotaishi  
> Created_at: 2026-02-06 15:29:19 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3861063278  

I am confused what you mean by "modularized in dependency order". Is there a specific requirement that the Boost libraries this library depends on must be moduarlised before `boost::beast` can?

---

## Comment 19

> Username: ashtum  
> Created_at: 2026-02-09 09:59:00 UTC  
> Url: https://github.com/boostorg/beast/pull/3068#issuecomment-3870677287  

> I am confused what you mean by "modularized in dependency order". Is there a specific requirement that the Boost libraries this library depends on must be moduarlised before `boost::beast` can?  
  
Yes, if we want to make `import std` work, all the libraries that Beast includes need to be modularized first. The issue is explained in more detail in Rubén’s blog post: https://anarthal.github.io/cppblog/modules4#_the_std_module

---
