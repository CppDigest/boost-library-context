# #908 S390x testing [Merged]

> Username: jzmaddock  
> Created at: 2022-12-28 18:34:39 UTC  
> Updated at: 2024-01-24 21:33:51 UTC  
> Merged at: 2023-01-11 18:31:05 UTC  
> Closed at: 2023-01-11 18:31:05 UTC  
> Url: https://github.com/boostorg/math/pull/908  



---

## Review 1 [Commented]

> Username: mborland  
> Created_at: 2023-01-07 23:35:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/908#pullrequestreview-1239714687  

📁 .drone.star

```diff
  53 |-     for cxx in clang_10_stds:
  54 |-       result.append(linux_cxx("Ubuntu clang++-10 " + cxx + " " + suite, "clang++-10", packages="clang-10", llvm_os="xenial", llvm_ver="10", buildtype="boost", image="cppalliance/droneubuntu1804:1", environment={'TOOLSET': 'clang', 'COMPILER': 'clang++-10', 'CXXSTD': cxx, 'TEST_SUITE': suite, }, globalenv=globalenv))
  39 |+       result.append(linux_cxx("Ubuntu g++ s390s " + cxx + " " + suite, "g++", packages="g++", buildtype="boost", image="cppalliance/droneubuntu2204:multiarch", arch="s390x", environment={'TOOLSET': 'gcc', 'COMPILER': 'g++', 'CXXSTD': cxx, 'TEST_SUITE': suite, }, globalenv=globalenv))
```

> Username: mborland  
> Created_at: 2023-01-07 23:35:43 UTC  
> Updated_at: 2023-01-07 23:35:49 UTC  
> Url: https://github.com/boostorg/math/pull/908#discussion_r1064059457  

Can we drop the number of standards tested to maybe just gnu++17 and c++17 until this is generally working? There have been complaints on the slack channel that we are sucking up all the bandwidth of s390x testing.

> Username: jzmaddock  
> Created_at: 2023-01-08 11:50:52 UTC  
> Url: https://github.com/boostorg/math/pull/908#discussion_r1064128930  

Apologies, it's normally me complaining about this kind of stuff too, will fix with the next commit.


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2023-01-08 12:01:47 UTC  
> Url: https://github.com/boostorg/math/pull/908#issuecomment-1374819173  

OK folks, this PR has fixed very nearly all the 128-bit long double related issues, nearly all so far have been testing-related rather than library related which is good.  @mborland , @NAThompson I wonder if I can draw your attention to the following last few failures, even hints on where to start looking would be useful:  
  
whittaker_shannon_test has a very large number of errors of the order 10^-25 which is 7162055944ULP which seems to be too large to ignore.  In logs at https://drone.cpp.al/boostorg/math/1080/27/2  
  
daubechies_scaling_test has errors of the order of 10^8ULP but only with 19 vanishing moments?  See log https://drone.cpp.al/boostorg/math/1080/27/2  
  
test_autodiff_2 and test_autodiff_3 have error rates that suggest truncation to double somewhere, but I simply don't know where to start looking: https://drone.cpp.al/boostorg/math/1080/38/2  
  
Thanks!

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-01-08 12:53:00 UTC  
> Url: https://github.com/boostorg/math/pull/908#issuecomment-1374829534  

OK in daubechies_scaling_test here: https://github.com/boostorg/math/blob/346d2a90f7f6f654794ad7d944e74eb7dc00378d/test/daubechies_scaling_test.cpp#L291 we have test data with insufficient precision.  Is this possible to obtain at higher precision or is it from the paper?  If the latter then I'll just disable testing that at 128-bit long double precision.

---

## Comment 4

> Username: mborland  
> Created_at: 2023-01-09 15:11:51 UTC  
> Url: https://github.com/boostorg/math/pull/908#issuecomment-1375769526  

> OK folks, this PR has fixed very nearly all the 128-bit long double related issues, nearly all so far have been testing-related rather than library related which is good. @mborland , @NAThompson I wonder if I can draw your attention to the following last few failures, even hints on where to start looking would be useful:  
>   
> whittaker_shannon_test has a very large number of errors of the order 10^-25 which is 7162055944ULP which seems to be too large to ignore. In logs at https://drone.cpp.al/boostorg/math/1080/27/2  
>   
> daubechies_scaling_test has errors of the order of 10^8ULP but only with 19 vanishing moments? See log https://drone.cpp.al/boostorg/math/1080/27/2  
>   
> test_autodiff_2 and test_autodiff_3 have error rates that suggest truncation to double somewhere, but I simply don't know where to start looking: https://drone.cpp.al/boostorg/math/1080/38/2  
>   
> Thanks!  
  
Nothing stands out to me as to why these have such high error. @pulver may be able to speak more intelligently to autodiff.

---

## Comment 5

> Username: pulver  
> Created_at: 2023-01-09 16:55:50 UTC  
> Url: https://github.com/boostorg/math/pull/908#issuecomment-1375945863  

> > test_autodiff_2 and test_autodiff_3 have error rates that suggest truncation to double somewhere, but I simply don't know where to start looking: https://drone.cpp.al/boostorg/math/1080/38/2  
>   
> @pulver may be able to speak more intelligently to autodiff.  
  
The 28 autodiff errors can be classified into 4 subsets that should have straight-forward fixes:  
  
1. `test_autodiff_2.cpp(334): error: in "test_autodiff_2/ylogx<__float128>":` The expected relative error is around 1.9e-32% but the actual appears to be around 3.3e-32%. In this case simply increasing `eps` from a factor of `100` to `300` to be safe should work.  
2. `exceeds 1.92593e-32%  
test_autodiff_2.cpp(541): error: in "test_autodiff_2/atan2_function<__float128>":` 25 of the 28 errors occur on this line. It looks like it is due to simply missing `using boost::math::atan2;` so that the `std` version was being called instead. (Assuming boost provides a 128-bit version of this function.) Similarly for the 2 errors below. For example the `acosh()` test is correctly written as it allows for proper [ADL](https://en.cppreference.com/w/cpp/language/adl) to occur.  
3. `test_autodiff_3.cpp(19): error: in "test_autodiff_3/atanh_test<__float128>":` Missing `using boost::math::atanh;`.  
4. `test_autodiff_3.cpp(53): error: in "test_autodiff_3/erf_test<__float128>":` Missing `using boost::math::erf;`.

---

## Comment 6

> Username: mborland  
> Created_at: 2023-01-10 00:49:53 UTC  
> Url: https://github.com/boostorg/math/pull/908#issuecomment-1376562531  

The autodiff tests are all green now; only interpolators remains.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2023-01-11 17:48:32 UTC  
> Url: https://github.com/boostorg/math/pull/908#issuecomment-1379263042  

Whew, this is all green now.  @mborland @NAThompson any reason not to go ahead and squash and merge?  Any pending PR's likely to conflict with this?

---

## Comment 8

> Username: mborland  
> Created_at: 2023-01-11 18:06:37 UTC  
> Url: https://github.com/boostorg/math/pull/908#issuecomment-1379286963  

I have no issues with this being merged.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2023-01-11 18:31:18 UTC  
> Url: https://github.com/boostorg/math/pull/908#issuecomment-1379316060  

OK good, merged.

---
