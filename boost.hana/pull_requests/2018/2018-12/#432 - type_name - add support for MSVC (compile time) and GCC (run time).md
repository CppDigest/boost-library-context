# #432 type_name : add support for MSVC (compile time) and GCC (run time) [Open]

> Username: pthom  
> Created at: 2018-12-20 22:03:37 UTC  
> Updated at: 2019-01-23 02:04:01 UTC  
> Url: https://github.com/boostorg/hana/pull/432  

This is my first PR ever to Boost.Hana, so I hope I did not make too many  
obvious mistakes. Please bare with me if I did.  
  
This PR extends the support of the experimental type_name feature to MSVC  
(compile time) and to GCC (run time only).  
  
All tests pass, see : https://travis-ci.org/pthom/hana/builds/470639623  
(The only failing test is due to a missing doxygen download.)  
  
One of the design choice of this PR was to make the inner work related to  
`__PRETTY_FUNCTION__` independent from Boost.   
There is an accompanying repo where it can be tested independently,   
in the hope that compiler implementers may take a look at it   
(see https://github.com/pthom/pretty_function_sandbox and   
https://travis-ci.org/pthom/pretty_function_sandbox/builds/470722255)  
  
Thanks.  
  
  
List of modified files:  
  
```  
include/boost/hana/experimental/  
├── detail  
│   ├── type_name_compiler_capabilities.hpp  
│   ├── type_name_pretty_function.hpp  
│   └── type_name_stringliteral.hpp  
├── type_name.hpp  
  
test/  
├── CMakeLists.txt  
    test/experimental/  
    ├── type_name.cpp  
    └── type_name_stringliteral_test.cpp  
```  
  
* include/boost/hana/experimental/detail  
  
    The design choices for the files in this folder is that they should  
    run without any dependency (not even a dependency on Boost.Hana).  
    The main API for them is inside type_name_pretty_function.hpp.  
  
    These files do not use the `BOOST_HANA_NAMESPACE_BEGIN` macro, in  
    order not to depend on Boost.Hana.   
  
    `test/experimental/type_name_stringliteral_test.cpp` is a  
    standalone test for these files.  
  
    The intent is to be able to provide them as testing tools for  
    compiler implementers, notably GCC which does not support  
    constexpr string literals.  
  
    This can be revised if needed of course.   
  
    The namespace in these files was renamed to `type_name_details::`  
  
    * type_name_pretty_function.hpp  
  
        * Provides  
        experimental::type_name_details::type_name_impl_stringliteral()  
  
        * Does this by defining a different prefix/suffix per compiler  
        (_HANA_TN_PRETTY_FUNCTION_TYPE_PREFIX,  
         _HANA_TN_PRETTY_FUNCTION_TYPE_SUFFIX)  
        * Also provides a compiler agnostic _HANA_TN__PRETTY_FUNCTION__  
  
    * type_name_compiler_capabilities.hpp :  
        Defines _HANA_TN_CAN_CONSTEXPR for MSVC and Clang  
        Implements _HANA_SIZEOF_OR_STRLEN as sizeof or strlen  
  
    * type_name_stringliteral.hpp :  
        Renames `cstring` to `stringliteral`  
        and adds some limited capabilities  
        (_HANA_TN_MAKE_STRINGLITERAL, stringliteral_equal,  
        stringliteral_equal_sz, stringliteral_to_string)  
  
* include/boost/hana/experimental/type_name.hpp  
    includes hana/experimental/detail/type_name_pretty_function.hpp  
    and uses `type_name_impl_stringliteral`  
    from type_name_pretty_function.hpp  
  
* test/experimental/type_name.cpp  
    contains the same tests as before, but it will be run under MSVC, Clang,  
    and GCC (runtime only).  
   There are two adaptations for MSVC, without a regex because it makes  
   it easier to understand the kind of output MSVC produces.  
  
* test/type_name_stringliteral_test.cpp  
    contains the same tests, except that  
    they are independent from Boost.Hana  
  
    It also adds another test concerning the fact that  
    __PRETTY_FUNCTION__ is west const on GCC/Clang/MSVC.  
    However I think this test is probably too much.  
  
* Those tests were re-enabled under GCC and MSVC

---

## Comment 1

> Username: pthom  
> Created_at: 2018-12-20 22:40:28 UTC  
> Url: https://github.com/boostorg/hana/pull/432#issuecomment-449158921  

This might close https://github.com/boostorg/hana/issues/410

---

## Comment 2

> Username: ricejasonf  
> Created_at: 2018-12-20 23:13:42 UTC  
> Url: https://github.com/boostorg/hana/pull/432#issuecomment-449167840  

I recall having a regex in `hana/experimental/printable.hpp` that stripped the superfluous `struct` keyword. It's definitely not constexpr though. I was unaware of `__cdecl`.  
  
It would be cool to normalize the output eventually, but maybe that is a bridge too far for this library.  
  
It's just a nitpick, but `inline constexpr` is redundant as constexpr functions are implicitly inline, and inline variables are not available until C++17 (where constexpr doesn't imply inline :confused:)

---

## Comment 3

> Username: pthom  
> Created_at: 2018-12-20 23:36:14 UTC  
> Updated_at: 2018-12-20 23:36:33 UTC  
> Url: https://github.com/boostorg/hana/pull/432#issuecomment-449175877  

Hi,   
  
Thanks for your answer!  
  
> It would be cool to normalize the output eventually, but maybe that is a bridge too far for this library.  
  
Well, I am working on this! I'm doing it in a separate project. It would not be reasonable to do it in a constexpr mode (although I tried for a while). I will let you know once I have something ready, if you are interested.   
  
> It's just a nitpick, but inline constexpr is redundant as constexpr functions are implicitly inline,   
  
So, should I replace `inline constexpr` by only `contexpr`. Is there a risk that it becomes not C++17 compliant in the near future?  
  
> and inline variables are not available until C++17 (where constexpr doesn't imply inline 😕)  
  
I recall that I just had an issue with Visual Studio 2017 where generic lambdas need to be declared `inline` in a header file (otherwise you get a duplicate symbol at link time). Presently, only MSVC enforces this, but I guess this might become the norm. Am I right?

---

## Comment 4

> Username: ricejasonf  
> Created_at: 2018-12-21 00:03:42 UTC  
> Updated_at: 2018-12-21 00:08:00 UTC  
> Url: https://github.com/boostorg/hana/pull/432#issuecomment-449182579  

>I recall that I just had an issue with Visual Studio 2017 where generic lambdas need to be declared inline in a header file (otherwise you get a duplicate symbol at link time). Presently, only MSVC enforces this, but I guess this might become the norm. Am I right?  
  
I was referring to the **function declarations** where `constexpr` implies inline. This is not the case for variables where C++17 introduces the `inline` specifier for **variable declarations**.  
  
You may notice that this library has many not inline `constexpr` variables, but apparently it is okay as it just means that their address would be different (ie not an ODR issue).  
  
Sorry, I don't mean to hijack your PR with that stuff. :sweat_smile:

---

## Comment 5

> Username: pthom  
> Created_at: 2018-12-22 11:55:52 UTC  
> Updated_at: 2018-12-22 11:56:10 UTC  
> Url: https://github.com/boostorg/hana/pull/432#issuecomment-449565077  

Well it seems that there is an issue with the CI and travis : my last commit ("ping travis" : https://github.com/boostorg/hana/pull/432/commits/37ab0dd4a45f2fdec9657a9acae0efff80e68e40) succeeds on my fork (see https://travis-ci.org/pthom/hana/builds/471039901), but was not launched on travis here.

---

## Comment 6

> Username: xqms  
> Created_at: 2019-01-13 01:01:17 UTC  
> Url: https://github.com/boostorg/hana/pull/432#issuecomment-453793268  

Hey, I *think* I found a solution that works compile-time both on clang and gcc:  
https://gist.github.com/xqms/6fd2a9bd776075df2b285be3321235cd  
  
Interacting with `__PRETTY_FUNCTION__` in gcc (tested on 7.3.0) feels *very* buggy, though.  
  
I will also add that I didn't test this code extensively yet.

---

## Comment 7

> Username: ricejasonf  
> Created_at: 2019-01-13 02:16:26 UTC  
> Url: https://github.com/boostorg/hana/pull/432#issuecomment-453796176  

Is this like https://github.com/Manu343726/ctti?

---

## Comment 8

> Username: xqms  
> Created_at: 2019-01-13 14:03:08 UTC  
> Url: https://github.com/boostorg/hana/pull/432#issuecomment-453832408  

Yes, similar technique, but seems to work also in static constexpr situations where ctti has problems (see #265 for discussion). Also, it returns a `hana::string` with characters in the type system, instead of a string_view-based object.  
  
I may have time to clean this up into a proper PR for discussion later this week - but if someone else is interested in doing that, go ahead ;-)

---

## Comment 9

> Username: pthom  
> Created_at: 2019-01-13 15:00:01 UTC  
> Url: https://github.com/boostorg/hana/pull/432#issuecomment-453836676  

@xqms :  thanks for your input!  
  
I do not have much time this sunday on this subject.   
I may have some time at the beginning of the week.  
  
Some questions and suggestions:  
  
  
#### Static constexpr situations  
You wrote:  
> but seems to work also in static constexpr situations where ctti has problems (see #265 for discussion)  
and  
  
Do you mean that you showed succesfully that ctti fails with gcc with in some situation and your code passes? If, some unit test showing this would be interesting!  
  
#### gcc and `__PRETTY_FUNCTION__`feels buggy:  
You wrote:  
> Interacting with __PRETTY_FUNCTION__ in gcc (tested on 7.3.0) feels very buggy, though  
Also would you be able to write some unit tests that fail under gcc and pass with other compilers?   
If so, they would be very useful.  
  
#### Minimal repository for tests with various compilers  
  
I have setup a minimal repository (with CI on travis and appveyor) for the tests  
here: https://github.com/pthom/pretty_function_sandbox  
  
There, I have setup CI tests with Clang linux, clang osx, gcc, and MSVC.  
  
And you can see the build logs here (they curently fail with GCC, and pass with MSVC and Clang)  
https://travis-ci.org/pthom/pretty_function_sandbox/builds/470722255?utm_source=github_status&utm_medium=notification  
  
Suggestion: during development, your idea could be tested in this repo instead of the full   
boost hana repo (so that we get a faster CI feedback). Would you be open to this suggestion?  
  
  
#### gcc  fix for __PRETTY_FUNCTION__   
In the bug link you provided (https://gcc.gnu.org/bugzilla/show_bug.cgi?id=66639), it is written that a fix was posted in gcc on Nov, 19 2018, so that may be the latest gcc release   
(7.4, December 6, 2018) finally provides a constexpr `__PRETTY_FUNCTION__`.   
This is also an interesting thing to test.

---

## Comment 10

> Username: xqms  
> Created_at: 2019-01-13 15:09:54 UTC  
> Url: https://github.com/boostorg/hana/pull/432#issuecomment-453837411  

@pthom Thanks for the feedback! I'll try to provide test cases later on.  
  
I think your idea of a separate sandbox is a good one - I'll open a PR there with my implementation.  
  
> In the bug link you provided (https://gcc.gnu.org/bugzilla/show_bug.cgi?id=66639), it is written that a fix was posted in gcc on Nov, 19 2018, so that may be the latest gcc release  
(7.4, December 6, 2018) finally provides a constexpr __PRETTY_FUNCTION__.  
  
I don't think so, because the fix was committed to trunk. See here for gcc branches & releases: https://gcc.gnu.org/develop.html#timeline  
To me, that means it'll end up in gcc 8. In my application, I'm stuck with 7.3 anyway, so I have high interest in a solution that works with that compiler.

---

## Comment 11

> Username: pthom  
> Created_at: 2019-01-14 10:26:20 UTC  
> Updated_at: 2019-01-14 10:29:02 UTC  
> Url: https://github.com/boostorg/hana/pull/432#issuecomment-453958492  

@xqms : please refer to the [gcc_sandbox branch](https://github.com/pthom/pretty_function_sandbox/tree/gcc_sandbox) on the sandbox repo : I made some adaptations in order to make it easy for you to work with it.  
  
Here, you will see that I [copy pasted](https://github.com/pthom/pretty_function_sandbox/blob/gcc_sandbox/detail/type_name_xqms_wip.hpp) your gist. I had to adapt it a little (since this repo does not include hana string, I used integer_sequence instead.) I added a first (very simple) test there : [xqms_test.cpp](https://github.com/pthom/pretty_function_sandbox/blob/gcc_sandbox/xqms_test.cpp)  
  
And the CI is configured on [travis](https://travis-ci.org/pthom/pretty_function_sandbox/branches)  (use the gcc_sandbox branch). The [latest build](https://travis-ci.org/pthom/pretty_function_sandbox/builds/479323335) shows a success with gcc, and a failure with MSVC (which can be solved later).  
  
Feel free to change anything in this branch (modify your code / add tests and examples, etc) there.  
  
Let's continue this discussion in the sandbox repo (when you have time, there is no urgency).

---

## Comment 12

> Username: pthom  
> Created_at: 2019-01-23 02:04:01 UTC  
> Url: https://github.com/boostorg/hana/pull/432#issuecomment-456639790  

@ricejasonf : since you were mentioning your interest in normalising the output, would you mind giving me your opinion about the [CleanType](http://code-ballads.net/cleantype/) library on which I am working?   
I did not yet communicate actively about it, since I was waiting to have a presentable version. I am presently trying to gather informed  inputs about it. If you are interested in giving me your input, please answer me by email or by comment in the article (in order to try not to pollute this PR).

---
