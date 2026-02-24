# #429 - Take a glance at GCC 4(.8) in CI [Closed]

> Username: ckormanyos  
> Created at: 2022-02-02 19:06:49 UTC  
> Updated at: 2022-05-11 04:26:03 UTC  
> Closed at: 2022-05-11 04:26:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/429  

Take a glance at GCC 4(.8) in CI and see how it goes. If it works well consider dealing with it as was done for GCC 5 in #425.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2022-03-24 07:43:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/429#issuecomment-1077328974  

This issue is phrased as a question. Does GCC 4(.8) run out-of-the-box on some CI tests when spun up in an (older LTS) Ubuntu CI machine? If so, consider adding such tests.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2022-05-07 11:52:43 UTC  
> Updated at: 2022-05-07 11:55:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/429#issuecomment-1120196359  

Here is my report on taking a glance at GCC 4.8 running on some CI tests.  
  
I found the following relevant issues.  
  
1. GCC 4.8 on Bionic does not recognize [this](https://github.com/boostorg/multiprecision/blob/ef6ad5e40f60cd3783de325031e8ce4a3ae6935d/include/boost/multiprecision/detail/number_base.hpp#L48) compiler check. I did [this](https://github.com/boostorg/multiprecision/blob/d20ee4079cd0f6c00d405c27822561262e16b80e/include/boost/multiprecision/detail/number_base.hpp#L48) potential workaround.  
2. GCC 4.8 on bionic does not auto-detect `BOOST_MATH_STANDALONE` ( in Math, not Multiprecision) as the default. So if I want to use that feature locally on 4.8, I need to specify that definition either on the command line or above the `#include`s.  
3. Some places in our code have mismatch `static constexpr` versus inline definition of `const` (in classes). Like [this here](https://github.com/boostorg/multiprecision/blob/ef6ad5e40f60cd3783de325031e8ce4a3ae6935d/include/boost/multiprecision/cpp_int.hpp#L525) where the class uses the attributes `static constexpr` but the code uses `const` only. So I needed to make sure the word `constexpr` agrees consistently in the class as well as the code. This happened about ten times. I corrected in my branch. There are a few in Math, but that's another topic. GCC 4.8 found these mismatches, whereas other tool chains did not.  
4. I can not get anything actually to run on GCC 4.8 in CI. I reduced CI to the candidate 4.8 job only, but I cant see any codes running.  
  
I would actually like help of point 4, since I would like to see something running. My separate projects do just fine with the above-mentioned changes in regard to running with Multiprecision together on 4.8.  
  
Point 3 raises the question if a `static const` POD class member ever actually _can_ have or need static linkage? I do not know.  
  
Now I had an agreement with Matt (@mborland) that 4.8 needs to run out-of-the-box, or I'll give up. i'm fine with giving up, but this thing is really close.  
  
Thoughts? Comments?  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 3

> Username: NAThompson  
> Created at: 2022-05-07 15:48:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/429#issuecomment-1120231008  

Just a quick question: What is the incentive for supporting gcc 4.8? Personally I'd like to see feature complete C++14 be the minimum supported standard.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2022-05-07 16:12:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/429#issuecomment-1120234703  

There's been some discussion about that on the mailing lists, which led to this thread: https://lists.boost.org/boost-users/2022/05/91268.php  Today's replies aren't in the archive yet, but there are some interesting data points, plus one private reply about needing boost.math with gcc-4.8 as it happens (they patched what they needed locally, and it wasn't too onerous).

---

## Comment 5

> Username: ckormanyos  
> Created at: 2022-05-07 16:58:55 UTC  
> Updated at: 2022-05-07 17:01:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/429#issuecomment-1120241116  

> What is the incentive for supporting gcc 4.8?  
  
Good question. It's not simply because it's there. There are some tangible reasons. I actually use several (embedded) compiler systems that got _stuck_ on 4.8. These include (among others) embedded free compilers for RX, RL78, V850 and TriCore.  
  
OK, mostly these are support for legacy, but I'm cutting code and benchmarking on these systems.  
  
> Personally I'd like to see feature complete C++14 be the minimum supported standard.  
  
This ushers in the (somewhat precocious and, let's say, bottom-line) sentiment: Oh 11, 14, 17, whatever, ... Dude, who cares, you're already not modern, 'cause it's basically 2023.  
  
So basically we are about 200 mouse clicks and keyboard strokes away from getting it back. So I really like C++11 and legacy, so I'm saying, why not?.  
  
One reason why not is because subsequently _carrying_ the extra baggage of that legacy compiler will hinder progress. This reason motivates me as well.  
  
So at the end of all the deliberations, I would like to get 4.8 back and retain it because:  
- Honestly, there is not much modern about Boost.  
- It is a classic, high-performance, portable service package.  
- So why lose those clients needing the 4.8, C++11 legacy?

---

## Comment 6

> Username: NAThompson  
> Created at: 2022-05-07 17:57:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/429#issuecomment-1120252819  

> So why lose those clients needing the 4.8, C++11 legacy?  
  
Yeah, there's no sense in intentionally breaking 4.8, but *supporting* it is quite a bit of work.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2022-05-07 18:25:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/429#issuecomment-1120258802  

With regard to tests not running, we have:  
  
```  
[ requires    
      cxx11_rvalue_references cxx11_template_aliases cxx11_hdr_array cxx11_allocator cxx11_constexpr cxx11_explicit_conversion_operators cxx11_ref_qualifiers  
      cxx11_hdr_functional cxx11_variadic_templates cxx11_user_defined_literals cxx11_decltype cxx11_static_assert cxx11_defaulted_functions  
      cxx11_noexcept cxx11_ref_qualifiers cxx11_user_defined_literals cxx11_hdr_type_traits  
      ]  
```  
  
And in the preamble for the tests I see:  
  
```  
 - cxx11_hdr_type_traits    : no [2]  
 ```  
  
Because gcc-4.x had an incomplete <type_traits> header.  However, there *may* be enough in there for our purposes, so I suggest removing `cxx11_hdr_type_traits` from the requires clause and see what happens, you would need to modify this as well: https://github.com/boostorg/multiprecision/blob/ef6ad5e40f60cd3783de325031e8ce4a3ae6935d/include/boost/multiprecision/detail/number_base.hpp#L48.

---

## Comment 8

> Username: ckormanyos  
> Created at: 2022-05-08 10:00:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/429#issuecomment-1120387639  

> suggest removing `cxx11_hdr_type_traits` from the requires clause and see what happens  
  
Thanks John. A lot of tests _did_ actually run. But (sigh of relief), 4.8 is, at the moment for me, too far out to reign back in. It's _only_ one or two things in tommath and a few good catches in Math. But the agreement was "run out of the box" or give up. So it's time to give up (at least until we may decide otherwise).  
  
So i am ready and willing to give up on GCC 4.8 (in Multiprecision), wirth the following findings and stipulations:  
  
1. There were a few places where `const` disagrees with `constexpr`. I found most of these and pushed them into my PR. I would like to retain these corrections.  
2. There is a similar correction desperately needed in Math at line 1012 of "unchecked_bernoulli.hpp". I did not modify Math since the Multiprecision PR is here. But I would like this correction in Math as well.  
3. I modified a bunch of header guards in Multiprecision, which were prefixed with `BOOST_MATH_`... and tried my best to fix these. John (@jzmaddock) it would be super-cool if you review/concur.  
4. I have separated the compiler checks (C++11) to a separate file called `detail/check_cpp11_config.hpp`. If we could in any way possible retain this file, it gives me (or others) a chance to use a lot of Multiprecision (at own risk) with GCC 4, whilst a local copy of this file is used/included/specialized locally in the project.  
  
Number 4 ends up being the biggest change. And I also forgot to remove the GCC 4 speialties from that new file `detail/check_cpp11_config.hpp`.  
  
So if we can agree on these items (or similar), then I'm more than happy to drop (at least for the moment) the GCC 4 strive. Anyway, we can see how the baseline 11/14 discussion goes and see if 11 is even going to be a future issue (I hope it will be), but that's open.  
  
Sorry for this long post.  
  
Comments? Thoughts?  
  
Cc: @jzmaddock and @mborland and @NAThompson
