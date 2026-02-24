# #22 Add constexpr support [Merged]

> Username: muggenhor  
> Created at: 2016-08-14 21:44:24 UTC  
> Updated at: 2018-02-08 16:41:34 UTC  
> Merged at: 2018-01-10 20:05:17 UTC  
> Closed at: 2018-01-10 20:05:17 UTC  
> Url: https://github.com/boostorg/units/pull/22  

This adds `constexpr` support to most of Boost.Units. Most importantly to the `unit` and `quantity` classes.  
  
This allows using these types in `constexpr` functions.  
  
I've tested this with GCC 5.4.0 and Clang 3.8.0 on Debian Stretch, compiling and running all the tests and examples for `-std=c++98`, `-std=c++03`, `-std=c++11`, `std=c++14`.  
  
My test runs of example/performance.cpp appeared to show a slight improvement for the ublas::matrix case compared to before this change. But the numbers seem to vary a lot so I suspect it's probably no significant difference at all. For Clang I do notice that with this change included, when compiling for C++11 or C++14 the size of the `performance` executable drops from 2.6M to 2.4M.  
  
Furthermore, when testing with Clang's `-emit-llvm` option with and without this changes shows that (global) constants (`static const quantity<some_unit> TheConstant`) now get constant propagated better. I.e. without this change a simple function returning the product of two constants would first get these from memory (`getelementptr`) and multiply them. With this change the result of the multiplication is inlined and returned directly.

---

## Comment 1

> Username: michaelbgratton  
> Created_at: 2017-06-16 15:26:04 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-309056302  

Has this issue been considered further?  I would find constexpr very helpful for handling static constant quantities.

---

## Comment 2

> Username: louis-langholtz  
> Created_at: 2017-06-16 15:34:55 UTC  
> Updated_at: 2017-06-16 15:38:22 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-309058666  

I don't know where this is in terms of the boost organization's acceptance of it but I've been using a fork of boost units that has muggenhor's `constexpr` support in it and seeing no problems in my use of it. I didn't get all test results to run but what I recall of that was that it had to do with my Travis/AppVeyor setup rather than the fork.

---

## Comment 3

> Username: swatanabe  
> Created_at: 2017-06-16 16:27:49 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-309071821  

AMDG  
  
I'll merge this soon.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: muggenhor  
> Created_at: 2017-06-30 17:58:01 UTC  
> Updated_at: 2017-07-01 14:02:09 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-312333714  

Note, I just went through this again: marking as `constexpr` of the `<cmath>` based stuff was a mistake, that depends on the standard library being properly marked constexpr for those things: it is not. So that portion should probably be reverted. Unless calling of non-constexpr functions from constexpr functions is only illegal when those constexpr functions get called from constexpr context. If it's legal outside of constexpr context then the current code is actually just future proof for when the standard library does "constexpr all the things" (i.e. becomes properly marked constexpr for everything only depending on arguments).  
  
So this requires two things to do:  
- [x] Find out if C++11 permits calling non-`constexpr` functions from `constexpr` functions provided those `constexpr` functions never get used in `constexpr` context  
- [ ] If it is not permitted: revert the `constexpr` additions from the math based portion  
  
I don't expect to have time for either of those until the end of next week at least.

---

## Comment 5

> Username: swatanabe  
> Created_at: 2017-06-30 18:37:52 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-312343193  

AMDG  
  
On 06/30/2017 11:58 AM, Giel van Schijndel wrote:  
> Note, I just went through this again: marking as `constexpr` of the `<cmath>` based stuff was a mistake, that depends on the standard library being properly marked constexpr for those things: it is not. So that portion should probably be reverted. Unless calling of non-constexpr functions from constexpr functions is only illegal when those constexpr functions get called from constexpr context. If it's legal outside of constexpr context then the current code is actually just future proof for when the standard library does "constexpr all the things" (i.e. becomes properly marked constexpr for everything only depending on arguments).  
>   
> So this requires two things to do:  
> - [ ] Find out if C++11 permits calling non-`constexpr` functions from `constexpr` functions provided those `constexpr` functions never get used in `constexpr` context  
  
It's allowed in this case:  
"If the instantiated template specialization of a constexpr  
function template or member function of a class template  
would fail to satisfy the requirements for a constexpr  
function or constexpr constructor, that specialization  
is not a constexpr function or constexpr constructor. [...]  
If no specialization of the template would yield a  
constexpr function or constexpr constructor, the program  
is ill-formed; no diagnostic required." [C++11 decl.constexp 7.1.5.6]  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: muggenhor  
> Created_at: 2017-07-01 14:03:05 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-312433838  

Thanks for investigating that. That makes it nicely forward compatible as well for when the underlying implementations do become `constexpr`.

---

## Comment 7

> Username: louis-langholtz  
> Created_at: 2017-07-02 20:13:25 UTC  
> Updated_at: 2017-07-02 20:13:49 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-312514295  

Is there any way it might actually add computational overhead to boost units?  
  
I'm seeing some overhead with a fork of this code that I'm using. I acknowledge that the overhead may have nothing to do with `constexpr` support however and I did find someone else [claiming to have seen overhead](https://www.codeproject.com/Articles/988932/Boost-Units-Library) presumably with the main branch of the units library.

---

## Comment 8

> Username: muggenhor  
> Created_at: 2017-07-02 20:57:30 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-312516529  

I expect this is likely caused by the amount of inlining done by the compiler. Because of the wrapper functions for the operators this adds at least one function call that needs to be inlined per operation. For expressions depending on the result of sub-expressions this requires the sub-expressions to be inlined first. As a result I expect the minimum amount of inlining passes to be able to properly optimize your code to be equal to the depth of the produced expression tree. For Clang I believe the number of inliner passes is three (if I recall Chandler Carruth's talk correctly, it something like "and the number of inlining shall be three" in it).

---

## Comment 9

> Username: joker-eph  
> Created_at: 2017-07-15 19:37:50 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-315557070  

I'm also interested in getting boost::unit being constexpr friendly! 👍    
  
@muggenhor Chandler's talk with the sentence about the number inlining being three was a joke, kind of saying "that's the obvious explanation".  However the issue was different in this case: it is that the information about the "reserve" for the vector propagates only with an interaction between two optimizations passes, and unless we iterate on these passes we can't get this code to optimize perfectly (it'll be fixed differently I believe).  
  
So clang has a limit on the number of inline passes on same function, it is four and it is *only* inside a SCC (i.e. in a set of mutually recursive functions) or when "devirtualization is happening".  Adding one extra level to a call-graph is not impact the inliner (it adds a little bit of compile-time though, but if these are inline functions the impact can be limited).

---

## Comment 10

> Username: joker-eph  
> Created_at: 2017-08-14 00:11:23 UTC  
> Updated_at: 2017-08-14 00:11:33 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-322076379  

What's missing to get this merged? @swatanabe @jhunold ?   
Can I help in any way?

---

## Comment 11

> Username: louis-langholtz  
> Created_at: 2017-10-28 16:48:06 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-340204262  

@muggenhor and @joker-eph Is there any command line parameter(s) I can give clang that will fix this? I have tried `-inline-threshold=10000`. That doesn't seem to effect slowdown I see when I build the [PlayRho](https://github.com/louis-langholtz/PlayRho) physics engine with boost units support enabled however. If it can't be fixed by command line options, I'm willing to experiment some with clang sources too if either of you can point me to likely places in the source to play with.

---

## Comment 12

> Username: joker-eph  
> Created_at: 2017-10-29 04:11:34 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-340236382  

> Is there any command line parameter(s) I can give clang that will fix this?  
  
Fix what?

---

## Comment 13

> Username: louis-langholtz  
> Created_at: 2017-10-29 04:59:49 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-340237865  

Sorry. I meant the computational overhead that I had asked about earlier in the thread on GitHub. You'd responded along with @muggenhor about limitations in clang that could be causing overhead in using boost units library with constexpr support.

---

## Comment 14

> Username: joker-eph  
> Created_at: 2017-10-29 05:13:59 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-340238335  

I doubt we'd see anything related to constexpr, but in general if you have a test case feel free to send it.

---

## Comment 15

> Username: louis-langholtz  
> Created_at: 2017-10-29 20:04:36 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-340289998  

@joker-eph Thank you for the suggestion. I'm afraid you have me at a bit of a loss however.  
  
I don't know how to make a simplified test case that would demonstrate @muggenhor's theory. It's his theory and while it sounds believable to me I don't feel qualified to write a test case for it.  
  
I also don't know how or to whom (like what email address or web-enabled bug tracking system) specifically that I'd send it to. I presume though, that in general terms,  you mean sending it to the LLVM project. Sounds like that's something you're more involved with than me  
  
It's also not clear to me that the overhead - about a 10% performance penalty is what I'm consistently seeing - has anything to do with @muggenhor 's `constexpr` enhancements, or whether it's something else like my usage (or possible miss-usage) of boost units. While I can easily compile away the boost units, or easily enough change compiler arguments, I am not able to easily switch to using the non-`constexpr`-enhanced - i.e. plain boost units. So I can test if the 10% hit goes away like it does when I compile without any units enforcement and was hoping that what @muggenhor explained was something there was a command line option for which I could use to test with.

---

## Comment 16

> Username: joker-eph  
> Created_at: 2017-10-30 07:00:09 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-340358965  

> I don't know how to make a simplified test case that would demonstrate @muggenhor's theory  
  
I was just asking about at test case that would show some runtime overhead with clang (irrespectively of the theory about why the runtime overhead is there). You could just attach it here or push it to a git repo (or to https://gist.github.com ) and post the URL here.

---

## Comment 17

> Username: louis-langholtz  
> Created_at: 2017-10-30 16:53:32 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-340509566  

@joker-eph If there's any clang/LLVM command line options that anyone can suggest beyond my having already tried `-inline-threshold=10000` I think that'd be easier for me to try out than for someone to try recreating the test scenarios to replicate the overhead finding.  
  
If anyone wants to check out the sources for the case I'm seeing overhead with when using these boost units however, they can be found at [PlayRho](https://github.com/louis-langholtz/PlayRho). This is a physics engine I've been developing and really zero-overhead compile-time enforced physical units is something I'd love for it. By default, the engine builds with these boost units disabled though because of the overhead I see when it is enabled. The difference can be seen in a few of the benchmark tests like the `TilesComesToRest` tests when run built without boost units (as built by default), and comparing those time results with results from when it's run and build with these boost units. I've made available a [guide for building with these boost units enabled](https://github.com/louis-langholtz/PlayRho/blob/master/Documentation/PhysicalUnits.md). I'm afraid the guide could be painfully difficult to follow. If you or anyone else tries it or even just identifies better ways it should use boost units however, I'd welcome feedback on the [PlayRho repo's issues interface](https://github.com/louis-langholtz/PlayRho/issues). If the overhead can be eliminated, I'll make it a more convenient CMake build option (along with other such options). Unfortunately making things easier to build in the engine will still require constexpr enhanced boost units to be part of the normal boost distribution as otherwise I suspect there'd be a rippling of places I'd have to get rid of `constexpr` from the engine to support boost units.

---

## Comment 18

> Username: michaelbgratton  
> Created_at: 2018-01-10 20:19:14 UTC  
> Url: https://github.com/boostorg/units/pull/22#issuecomment-356724103  

Thanks! I'm looking forward to using the feature.

---
