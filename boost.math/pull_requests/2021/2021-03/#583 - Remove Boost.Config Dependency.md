# #583 Remove Boost.Config Dependency [Merged]

> Username: mborland  
> Created at: 2021-03-26 17:30:54 UTC  
> Updated at: 2021-03-29 16:36:48 UTC  
> Merged at: 2021-03-28 18:13:56 UTC  
> Closed at: 2021-03-28 18:13:56 UTC  
> Url: https://github.com/boostorg/math/pull/583  

- Removes all uses of Config except in `<boost/math/tools/config.hpp>` which is behind a standalone guard  
- Temporarily removes TR1 tests. I cannot get them to be ignored by standalone mode tests  
- Converts some of the tests in the GHA CI to test standalone mode

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-26 18:52:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/583#pullrequestreview-622449588  

📁 doc/background/special_tut.qbk

```diff
 369 |-    #ifndef BOOST_MATH_NO_REAL_CONCEPT_TESTS
 370 |-    #if !BOOST_WORKAROUND(BOOST_BORLANDC, BOOST_TESTED_AT(0x582))
 371 |-       test(boost::math::concepts::real_concept(0.1), "real_concept");
```

> Username: jzmaddock  
> Created_at: 2021-03-26 18:52:48 UTC  
> Updated_at: 2021-03-27 16:29:23 UTC  
> Url: https://github.com/boostorg/math/pull/583#discussion_r602522054  

The BOOST_WORKAROUND line can go, but this line should really stay?


---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-26 18:53:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/583#pullrequestreview-622450312  

📁 doc/html/math_toolkit/lambert_w.html

```diff
1404 |       The arrays are as <code class="computeroutput"><span class="keyword">const</span></code> and <code class="computeroutput"><span class="keyword">constexpr</span></code> and <code class="computeroutput"><span class="keyword">static</span></code>
1405 |-       as possible (for the compiler version), using BOOST_STATIC_CONSTEXPR macro.
1405 |+       as possible (for the compiler version), using static constexpr macro.
```

> Username: jzmaddock  
> Created_at: 2021-03-26 18:53:51 UTC  
> Updated_at: 2021-03-27 16:29:23 UTC  
> Url: https://github.com/boostorg/math/pull/583#discussion_r602522635  

Typo: `static constexpr` is not a macro (any more).


---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-26 18:56:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/583#pullrequestreview-622452693  

📁 example/handle_test_result.hpp

```diff
 120 | {
 121 |- #ifdef BOOST_MSVC
 121 |+ #ifdef _MSC_VER
```

> Username: jzmaddock  
> Created_at: 2021-03-26 18:56:58 UTC  
> Updated_at: 2021-03-27 16:29:23 UTC  
> Url: https://github.com/boostorg/math/pull/583#discussion_r602524416  

There is a potential gotcha here, in that some compilers not msvc define _MSC_VER, hopefully this won't be an issue, but just a heads up...

> Username: mborland  
> Created_at: 2021-03-27 06:53:05 UTC  
> Updated_at: 2021-03-27 16:29:23 UTC  
> Url: https://github.com/boostorg/math/pull/583#discussion_r602680838  

I was under the impression that only MSVC defined `_MSC_VER`. Is there something more robust I should replace this with? Some combination like `defined(_MSC_VER) && defined(_MSVC_LANG)`?


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-26 19:13:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/583#pullrequestreview-622464092  

📁 include/boost/math/quadrature/trapezoidal.hpp

```diff
 113 | }
 114 |- #if BOOST_WORKAROUND(BOOST_MSVC, < 1800)
 114 |+ #if defined(_MSC_VER) && (_MSC_VER < 1800)
```

> Username: jzmaddock  
> Created_at: 2021-03-26 19:13:38 UTC  
> Updated_at: 2021-03-27 16:29:23 UTC  
> Url: https://github.com/boostorg/math/pull/583#discussion_r602533159  

This was a workaround for vc-11 and earlier: that compiler was not C++11 compliant or anywhere near it and won't be supported any more, so this whole #if branch can go.


---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-03-26 19:45:08 UTC  
> Url: https://github.com/boostorg/math/pull/583#issuecomment-808470482  

Great stuff Matt!  I thought you were quiet, looks like you've been busy :)  
  
I must confess I've been wondering about testing standalone mode.... seems like in order to ensure that we're not inadvertently #including something that we shouldn't, then the only effective test is one that does not install any Boost headers from other libraries.  That's probably quite tricky to figure out since we do need Boost.Build.  Also means we can't build tests which depend on Boost.Test or others.  However, the header-inclusion tests *should* be buildable without the rest of Boost present.  We might need to refactor the Jamfile a bit to put all the tests which build standalone together.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2021-03-27 06:54:58 UTC  
> Updated_at: 2021-03-27 06:55:34 UTC  
> Url: https://github.com/boostorg/math/pull/583#issuecomment-808673555  

> wondering about testing standalone mode.... seems like in order to ensure that we're not inadvertently #including something that we shouldn't, then the only effective test is one that does not install any Boost headers from other libraries.  
  
This might seem naive, but when testing standalone in CI, you could identify a separate standalone area for `../boost-standalone` or similar. There you install on the fly the required submodules. But then in this place, _do not_ run `.b2 headers`. Instead, put the `libs/math/include` paths of the needed submodules on the command line for the compiler run.  
  
This leaves the problem of Boost.Test. You would have to not use it. Use a small collection of self-written abstractions instead for close ratio, signedness, etc.  
  
I am doing similar things _on the metal_ and we will eventually need to synchronize these methods, if we do pursue this endeavor.

---

## Comment 7

> Username: mborland  
> Created_at: 2021-03-27 07:23:59 UTC  
> Url: https://github.com/boostorg/math/pull/583#issuecomment-808678479  

> I must confess I've been wondering about testing standalone mode.... seems like in order to ensure that we're not inadvertently #including something that we shouldn't, then the only effective test is one that does not install any Boost headers from other libraries. That's probably quite tricky to figure out since we do need Boost.Build. Also means we can't build tests which depend on Boost.Test or others. However, the header-inclusion tests _should_ be buildable without the rest of Boost present. We might need to refactor the Jamfile a bit to put all the tests which build standalone together  
  
I was unsure of how to disable certain tests in Boost.Build if `BOOST_MATH_STANDALONE` was defined which is why I stripped out the TR1 tests. To truly test standalone mode wouldn't we need to move away from Boost.Build to something like CMake? I believe @pabristow asked about doing something to that affect in the ML with no response. I feel like it is a fair assumption that if someone is going to download Boost.Math from the github page to use in standalone they will use it with CMake:  
  
```  
find_package(boost_math)  
target_compile_definitions(my_target PRIVATE BOOST_MATH_STANDALONE=1)  
```  
Soon here there should be no required boost submodules so I could see @ckormanyos suggestion of keeping Boost.Build and just building somewhere else as working.

---

## Comment 8

> Username: mborland  
> Created_at: 2021-03-27 09:24:18 UTC  
> Url: https://github.com/boostorg/math/pull/583#issuecomment-808699486  

Running the compile tests using CMake has been added in 860169d. All you need to do to run these tests is `cmake -DBUILD_TESTING=1 . && make -jxx`

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2021-03-27 10:24:38 UTC  
> Updated_at: 2021-03-27 10:25:23 UTC  
> Url: https://github.com/boostorg/math/pull/583#issuecomment-808707663  

> Soon here there should be no required boost submodules...  
  
What an excellent achievement! Awesome!  
  
> suggestion of keeping Boost.Build and just building somewhere else as working.  
  
At that stage it's conceivable (if you are so inclined) to dispense with bjam, CMake, GNUmake, MSBuild, and the whole lot and just hammer the simple command lines onto the OS of the CI pipeline. I have a whole collection of command lines for `cl.exe` in some other non-published projects, these being somewhat less-known than the more familiar GCC command lines.

---

## Comment 10

> Username: mborland  
> Created_at: 2021-03-27 12:18:15 UTC  
> Url: https://github.com/boostorg/math/pull/583#issuecomment-808724022  

> At that stage it's conceivable (if you are so inclined) to dispense with bjam, CMake, GNUmake, MSBuild, and the whole lot and just hammer the simple command lines onto the OS of the CI pipeline. I have a whole collection of command lines for `cl.exe` in some other non-published projects, these being somewhat less-known than the more familiar GCC command lines.  
  
I think using something like CMake will be easier for us and the end user. The way the `CMakeLists.txt` is written just adding a file to the `compile_tests` folder causes it to be tested.

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2021-03-27 13:06:47 UTC  
> Url: https://github.com/boostorg/math/pull/583#issuecomment-808730330  

> I think using something like CMake will be easier for us and the end user. The way the `CMakeLists.txt` is written just adding a file to the compile_tests folder causes it to be tested.  
  
OK. Affirmative. I will comply to that in whatever progress I make in bare metal as well.

---

## Comment 12

> Username: mborland  
> Created_at: 2021-03-27 16:32:30 UTC  
> Url: https://github.com/boostorg/math/pull/583#issuecomment-808758587  

I reverted the CI system to what is already in. There are still other dependencies that need to be addressed (e.g. lexical_cast) so I saved the yml and figure it would be better to implement down the line. I'll give the regular CI another cycle because there is a lot change here, but then should be good to go.

---

## Comment 13

> Username: mborland  
> Created_at: 2021-03-28 06:22:28 UTC  
> Url: https://github.com/boostorg/math/pull/583#issuecomment-808854287  

The only CI failure is once again MSVC 14.2 calculating gini coefficients. Not sure why this has recently become an issue.

---

## Comment 14

> Username: pabristow  
> Created_at: 2021-03-28 09:34:42 UTC  
> Url: https://github.com/boostorg/math/pull/583#issuecomment-808871657  

I didn't get any convincing replies to my query about B2/bjam and CMake, but I concluded that   
  
*users* find CMake convenient to create stuff to make their IDE run (MSVC solutions and project, Codeblocks, Eclipse...),  
  
*developers and testers* find B2/bjam was invaluable for  multiple platforms, compilers (and their versions), C++ standards etc with jamfiles like b2 toolset=msvc14.2,msvc14.3,gcc-11.0,clang-11.0.1 cxxstd=11,14,17,20 ...  I haven't seen how CMake could do that.  
  
So the short answer is that both are needed: CMake for Users, and B2/Bjam for developers.  
  
(Of course testing and using different hardware, especially the many novel 'bare metal' devices, beloved of Chris, is another big can of worms that is rightly becoming more important).

---

## Comment 15

> Username: mborland  
> Created_at: 2021-03-28 18:02:45 UTC  
> Url: https://github.com/boostorg/math/pull/583#issuecomment-808933681  

@jzmaddock Do you have any more changes for this one? Once this is in I can open 2x PRs (lexical cast + core, and integer) to round out standalone implementation. I think after those final two CMake powered standalone CI can be re-added to integrate actual testing on standalone. I left the structure for what CMake needs to run the compile tests and examples in here.

---
