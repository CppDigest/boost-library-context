# #65 Tensor Extension for uBLAS [Merged]

> Username: bassoy  
> Created at: 2019-02-19 11:18:51 UTC  
> Updated at: 2019-02-26 07:13:21 UTC  
> Merged at: 2019-02-26 07:13:21 UTC  
> Closed at: 2019-02-26 07:13:21 UTC  
> Url: https://github.com/boostorg/ublas/pull/65  

This feature branch extends uBLAS with tensors. It provides basic contraction operations and also supports the Einstein summation notation. You can find further information at [tensor wiki](https://github.com/BoostGSoC18/tensor/wiki).  
  
Boost.uBLAS runs with C++11 and C++17 standard  
Tested with MCVS 14.1, 14.0 and GCC 7.4.

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:34:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-205212144  

📁 include/boost/numeric/ublas/matrix.hpp

```diff
 264 |+ 	 * \return a reference to the element
 265 |+ 	 */
 266 |+ 		BOOST_UBLAS_INLINE
```

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:34:58 UTC  
> Updated_at: 2019-02-25 16:05:25 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258039463  

The indentation of this block of code is off. Please adjust to match the rest of the code. (The same comment applies to a few other places.)

> Username: bassoy  
> Created_at: 2019-02-19 14:35:48 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258065335  

Will use clang-format now for that purpose.

> Username: bassoy  
> Created_at: 2019-02-20 08:01:27 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258368123  

I have not used clang-format. Need to aggree on the format first. So I have used qtcreator for that purpose. But the indentation is much nicer and more consistent now.


---

## Review 2 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:39:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-205214221  

📁 test/tensor/test_expression.cpp

```diff
  26 |+ 
  27 |+ 
  28 |+ struct fixture {
```

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:39:26 UTC  
> Updated_at: 2019-02-25 16:05:25 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258041111  

A general formatting comment: Please start a new line for opening braces, i.e.  
```  
struct fixture  
{  
    ...  
}  
```

> Username: bassoy  
> Created_at: 2019-02-19 14:36:04 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258065488  

Will use clang-format now for that purpose.

> Username: bassoy  
> Created_at: 2019-02-20 08:02:26 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258368347  

Again qtcreator used for that. It is now consistent.


---

## Review 3 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:40:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-205214667  

📁 test/tensor/test_expression.cpp

```diff
  29 |+ 	using extents_type = boost::numeric::ublas::shape;
  30 |+ 	fixture() : extents{
  31 |+ 								extents_type{},            // 0
```

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:40:21 UTC  
> Updated_at: 2019-02-25 16:05:25 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258041530  

Same formatting (newlines before braces), plus remove excessive indentation.

> Username: bassoy  
> Created_at: 2019-02-19 14:36:10 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258065541  

Will use clang-format now for that purpose.


---

## Review 4 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:46:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-205217954  

📁 include/boost/numeric/ublas/matrix_expression.hpp

```diff
  89 | #endif
  90 | 
  91 |+ #ifndef BOOST_UBLAS_REFERENCE_CONST_MEMBER
```

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:46:52 UTC  
> Updated_at: 2019-02-25 16:05:25 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258044151  

You added a number of new accessors to existing types. Do you have tests for them ?

> Username: bassoy  
> Created_at: 2019-02-19 14:38:15 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258066520  

Yes. I have tests in test/tensor. Yet there are no unit-tests in ublas testing purely expression templates.

> Username: bassoy  
> Created_at: 2019-02-20 08:03:34 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258368608  

I will mark this as resolved now. There is no simple solution to unit-testing matrix expression templates.


---

## Review 5 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:48:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-205218538  

📁 examples/tensor/Jamfile

```diff
  18 |+     ;
  19 |+ 
  20 |+ exe example_construction_access : example_construction_access.cpp ;
```

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:48:00 UTC  
> Updated_at: 2019-02-25 16:05:25 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258044670  

Just for terseness, I would suggest not to repeat the word `example` in the example source files (and executable names). The user knows he is looking at example code in this directory. :-)

> Username: bassoy  
> Created_at: 2019-02-19 14:39:24 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258067055  

the user might forget that. you never know at what time you program :-)

> Username: bassoy  
> Created_at: 2019-02-24 11:10:57 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259615098  

this has been fixed now.


---

## Review 6 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:48:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-205218966  

📁 README.md

```diff
   4 | 
   4 |- # Building [![Build Status](https://travis-ci.org/boostorg/ublas.svg?branch=develop)](https://travis-ci.org/boostorg/ublas) [![Build status](https://ci.appveyor.com/api/projects/status/ctu3wnfowa627ful/branch/develop?svg=true)](https://ci.appveyor.com/project/stefanseefeld/ublas/branch/develop)
   5 |+ ## Fork
```

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:48:50 UTC  
> Updated_at: 2019-02-25 16:05:25 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258045014  

The docs need to be updated to reflect that this is no longer a fork. :-)

> Username: bassoy  
> Created_at: 2019-02-19 14:39:34 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258067143  

Yes. Will change that.

> Username: bassoy  
> Created_at: 2019-02-20 08:03:51 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258368672  

Done.


---

## Review 7 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:50:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-205219962  

📁 Jamfile

```diff
   1 |+ # Boost.uBLAS
```

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:50:48 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258045798  

Actually, I'm not convinced this file should be here. (Sorry to first asking you to rename it !)  
The present logic assumes that running `./b2` on the project should build tensor tests and tensor examples, which probably was a nice convenience for you during development, but doens't make much sense now.  
The `test/` and `examples/` subdirectories are better integration point to inject the specific build logic for tests and examples respectively.

> Username: bassoy  
> Created_at: 2019-02-19 14:42:26 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258068499  

The Boost Build [Documentation](https://boostorg.github.io/build/manual/develop/index.html#bbv2.tutorial.hierarchy) tells something different. We need to clarify this point.

> Username: stefanseefeld  
> Created_at: 2019-02-19 23:14:37 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258271852  

See https://github.com/boostorg/gil/blob/develop/.ci/build-and-test.sh#L37-L40 for an example where the test logic iterates over specific (extension) test subdirectories explicitly. We should do the same here.

> Username: stefanseefeld  
> Created_at: 2019-02-19 23:46:21 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258279544  

And here is an example of how to conditionally include a target when the `cxxstd` property has a specific value: https://github.com/boostorg/poly_collection/blob/develop/example/Jamfile.v2#L14-L17 (That technique can be used to conditionalize an entire project, too, so I'd suggest you define a project for the entire test/tensors/ subdirectory, as I have done in https://github.com/boostorg/ublas/blob/develop/test/opencl/Jamfile

> Username: bassoy  
> Created_at: 2019-02-24 17:17:27 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259630785  

I changed everything. However, e.g. GIL has also a Jamfile at the root directory. I am wondering why we don't have https://github.com/boostorg/gil/blob/develop/Jamfile

> Username: stefanseefeld  
> Created_at: 2019-02-24 22:14:42 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259643707  

Well, in the case of GIL, it seems we use that toplevel Jamfile to indicate common compiler flags to be used in all sub-projects. The only other effect that Jamfile has is to allow you to run `b2 libs/gil` as an alias to `b2 libs/gil/test`.  
In our case here we have more than tests, so the question is what you'd expect `b2 libs/numeric/ublas` to do ? Build everything, i.e. tests, benchmarks, examples ? Only tests ? It's at best ambiguous, so I'd opt for being explicit, i.e. requiring users to decide themselves what they want to build (and run).

> Username: bassoy  
> Created_at: 2019-02-24 23:54:41 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259649011  

okay leaving this issue to be solved on the forked repo.

> Username: bassoy  
> Created_at: 2019-02-25 15:33:25 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259879327  

okay removed.


---

## Review 8 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:51:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-205220369  

📁 .travis.yml

```diff
   2 | # Distributed under the Boost Software License, Version 1.0.
   3 | # (See accompanying file LICENSE_1_0.txt or copy at http://boost.org/LICENSE_1_0.txt)
   4 |+ 
```

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:51:34 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258046106  

This file shouldn't contain any changes, other than additional build variants for C++17 - specific builds.


---

## Review 9 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:51:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-205220517  

📁 .appveyor.yml

```diff
  11 |     - master
  12 |     - develop
  13 |+     - tensor
```

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:51:52 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258046215  

This file shouldn't contain any changes, other than additional build variants for C++17 - specific builds.

> Username: bassoy  
> Created_at: 2019-02-19 14:45:31 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258069969  

I have the same comments as for .travis.yml. We need to clarify this point.

> Username: bassoy  
> Created_at: 2019-02-20 07:56:18 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258366836  

The tensor branch is taken away. Still need to add build variants.


---

## Review 10 [Commented]

> Username: bassoy  
> Created_at: 2019-02-19 14:44:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-205250279  

📁 .travis.yml

```diff
  13 |     - develop
  14 |+     - tensor
  15 |+     - subtensor
```

> Username: bassoy  
> Created_at: 2019-02-19 14:44:12 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258069349  

I aggree on taking away the branches.

> Username: bassoy  
> Created_at: 2019-02-20 07:54:55 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258366534  

Done.


---

## Review 11 [Commented]

> Username: bassoy  
> Created_at: 2019-02-19 14:44:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-205250616  

📁 .travis.yml

```diff
  28 |     - os: linux
  25 |-       env: TOOLSET=gcc COMPILER=g++-7 CXXSTD=c++11
  29 |+       env: TOOLSET=gcc COMPILER=g++-7 CXXSTD=c++17
```

> Username: bassoy  
> Created_at: 2019-02-19 14:44:42 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258069605  

But here we should test it with gcc and clang and also different c++ standards right?

> Username: stefanseefeld  
> Created_at: 2019-02-19 23:11:43 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r258271078  

Yes, exactly. (at least) one with c++11 and one with c++17, as a start. We can add other compilers later. Then, in the tensor test Jamfile, we can conditionalize the tests on that. (In fact, we should use the predefined property `cxxstd` for that, as that makes it easy to express the logic of "only do this when `cxxstd=17`, etc.


---

## Comment 12

> Username: yimyom  
> Created_at: 2019-02-19 22:58:09 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#issuecomment-465346178  

as it is a massive pull request, I will trust you both on this. Is it ok to merge it now ?

---

## Comment 13

> Username: stefanseefeld  
> Created_at: 2019-02-19 23:08:20 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#issuecomment-465349348  

We are working on it...

---

## Review 14 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-24 16:58:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-207161539  

📁 test/tensor/test_operators_arithmetic.cpp

```diff
  43 |+ };
  44 |+ 
  45 |+ BOOST_AUTO_TEST_SUITE(test_tensor_arithmetic_operations, * boost::unit_test::depends_on("test_tensor"));
```

> Username: stefanseefeld  
> Created_at: 2019-02-24 16:58:20 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259630041  

It seems the trailing `;` is redundant. (There are lots of warnings from the travis build about this.) There may be similar issues in other places...

> Username: bassoy  
> Created_at: 2019-02-24 17:21:50 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259630839  

Well it seems. If I leave it out. It did not compile really.

> Username: bassoy  
> Created_at: 2019-02-24 23:55:11 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259649031  

updated now.


---

## Comment 15

> Username: bassoy  
> Created_at: 2019-02-24 18:58:43 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#issuecomment-466805314  

last commits are squashed into one.

---

## Review 16 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-24 22:02:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-207174512  

📁 .appveyor.yml

```diff
  16 |   matrix:
  17 |-     - APPVEYOR_BUILD_WORKER_IMAGE: Visual Studio 2015
  18 |-       TOOLSET: msvc-14.0
```

> Username: stefanseefeld  
> Created_at: 2019-02-24 22:02:20 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259643118  

I suggest to leave that compiler, and only conditionalize the `tensor` builds if that relies on a more recent MSVC version.

> Username: bassoy  
> Created_at: 2019-02-24 23:54:06 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259648979  

leaving this to be solved on the boostorg repo.


---

## Review 17 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-24 22:19:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-207175418  

📁 include/boost/numeric/ublas/operation/begin.hpp

```diff
 312 |-         return it.begin();
 313 |-     }
  31 |+ 		    /**
```

> Username: stefanseefeld  
> Created_at: 2019-02-24 22:19:56 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259643927  

It looks like the only change to this file is to change the number of spaces in indentation, right ?  
I would prefer not to add such changes as they are independent of your work on tensor support.  
(In addition, I really think this is way too much indentation, as it doesn't leave much room for actual content, if we want to limit the line length.)

> Username: bassoy  
> Created_at: 2019-02-24 23:07:14 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259646578  

the indentation is a tab and github's edit decides to put 4 spaces for one tab. so you can adjust it as you want on any ide and text editor.

> Username: stefanseefeld  
> Created_at: 2019-02-24 23:12:13 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259646819  

Ah, but we shouldn't use tabs at all ! (Really, all this adds to my argument not to mess with existing code formatting in the context of the current PR.)

> Username: bassoy  
> Created_at: 2019-02-24 23:56:17 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259649075  

okay. no tabs any more :-)


---

## Review 18 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-24 22:23:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-207175618  

📁 test/Jamfile

```diff
  53 |        <toolset>vacpp:<define>"BOOST_UBLAS_NO_ELEMENT_PROXIES"
  54 |-        <toolset>gcc:<cxxflags>"-Wall -pedantic -Wextra"
  54 |+        <toolset>gcc:<cxxflags>"-Wall -pedantic -Wextra -Wno-unknown-pragmas -std=c++17"
```

> Username: stefanseefeld  
> Created_at: 2019-02-24 22:23:44 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259644173  

This looks wrong: You are adding `-std=c++17` if the compiler is `gcc`. You shouldn't change the flag (but filter on it in `test/tensor/Jamfile`, as you actually do now. :-) )

> Username: bassoy  
> Created_at: 2019-02-24 23:46:22 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259648542  

true. changed it.


---

## Review 19 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-24 22:27:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-207175793  

📁 include/boost/numeric/ublas/matrix_sparse.hpp

```diff
 370 |         BOOST_UBLAS_INLINE
 371 |-         void reserve (size_type non_zeros, bool preserve = true) {
 371 |+ 				void reserve (size_type non_zeros, bool /*preserve = true*/) {
```

> Username: stefanseefeld  
> Created_at: 2019-02-24 22:27:25 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259644326  

please don't change the indentation. (this might be from your editor's config. Please don't do that. :-) )

> Username: stefanseefeld  
> Created_at: 2019-02-24 22:45:18 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259645517  

Oh, and there is also a bug: your change removes the default value, so existing code calling `reserve(N)` will presumably no longer work. You should comment out only the argument name, e.g.  
"void reserve(size_type non_zeros, bool /*preserve*/ = true)".

> Username: bassoy  
> Created_at: 2019-02-24 22:53:56 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259645965  

true. changed it.

> Username: stefanseefeld  
> Created_at: 2019-02-24 23:10:19 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259646729  

Perhaps, but do you know all the code that calls this function ? :-)

> Username: bassoy  
> Created_at: 2019-02-24 23:13:55 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259646922  

no, but it does not change if the calling function sets preserve, it does not have an effect. Actually it should be commented out to indicate that it is not used.

> Username: stefanseefeld  
> Created_at: 2019-02-24 23:21:07 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259647272  

I understand. My point is: if a user calls the function with two arguments, it now generates a compilation error, because after your change the function only takes one argument.  
(Correction: if a user calls the function with **one** argument...)


---

## Review 20 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-25 15:40:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/65#pullrequestreview-207464846  

📁 README.md

```diff
  35 |+ | `examples`  | example files                  |
  36 |+ | `include`   | headers                        |
  37 |+ | `test`      | unit tests                     |
```

> Username: stefanseefeld  
> Created_at: 2019-02-25 15:40:24 UTC  
> Updated_at: 2019-02-25 16:05:26 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259882741  

Would you mind adding the `benchmarks` directory to the list ?

> Username: bassoy  
> Created_at: 2019-02-25 15:49:38 UTC  
> Updated_at: 2019-02-25 16:07:21 UTC  
> Url: https://github.com/boostorg/ublas/pull/65#discussion_r259887333  

done.


---
