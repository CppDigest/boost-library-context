# #9 Feature: hyperexponential distribution [Closed]

> Username: sguazt  
> Created at: 2014-09-09 20:11:36 UTC  
> Updated at: 2015-04-03 14:48:00 UTC  
> Closed at: 2015-04-01 19:41:36 UTC  
> Url: https://github.com/boostorg/random/pull/9  

Hello,  
I've recently added the hyperexponential distribution to Boost.Math/Statistical Distribution (with the precious feedback of John Maddock and Paul Bristow).  
Here is the pull request log: https://github.com/boostorg/math/pull/7  
The contribution has been already  accepted and currenlty it is in the `develop` branch (there may be some additional refinements, but the class is pretty stable).  
  
I thought that it would be very nice to have in Boost the random variate generator counterpart.  
  
So, here it is :)  
  
If you like this idea, can you consider this pull request?  
  
The development includes the random generator class + doxygen doc and two test units.  
Note that I had to write my own test units in a similar fashion to what has be done for the `discrete_distribution`).  
  
Thank you so much.  
  
Best,  
  Marco

---

## Comment 1

> Username: swatanabe  
> Created_at: 2014-09-09 21:45:40 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-55040832  

AMDG  
- Don't use the unnamed namespace in headers  
- I think check_probabilities can fail for large  
  vectors, even if there's no real issue.  
- In the tests, I really dislike the way you've  
  overloaded normalize on constness.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: sguazt  
> Created_at: 2014-09-10 06:56:54 UTC  
> Updated_at: 2014-09-10 15:54:27 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-55077908  

Hi Steven,  
Thank you for the feedback.  
  
I've tried to fix the above issues:  
- Remove unnamed namespace from include file  
- Improved the `check_probabilities` as well as the `normalize` functions  
- Renamed the overloaded version of `normalize` to `normalized_copy` in test file  
  
By the way, can you explain why unnamed namespace is not good in header files?  
  
Cheers,  
 Marco

---

## Comment 3

> Username: swatanabe  
> Created_at: 2014-09-10 16:55:58 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-55146125  

AMDG  
  
On 09/10/2014 12:56 AM, Marco Guazzone wrote:  
  
> To this end, can you explain why unnamed namespace is not good in header files?  
  
The unnamed namespace is unique for every translation unit,  
causing ODR violations and code bloat.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: sguazt  
> Created_at: 2014-09-10 17:16:30 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-55149194  

My original intention in using unnamed namespace was to _hide_ details used only inside a header file.  
I thought that header guards (i.e., the `#ifndef` / `#define` / `#endif` amigos surrounding the whole header content) were sufficient to guarantee ODR. But, it seems I'm wrong...  
  
Cheers,  
  Marco

---

## Comment 5

> Username: sguazt  
> Created_at: 2014-09-30 08:08:32 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-57280300  

Hi Steven,  
  
Sorry to bother you.  
Any more feedback on this pull request?  
I admit I'd like to see it in the next Boost version :-)  
  
Cheers,  
Marco

---

## Comment 6

> Username: swatanabe  
> Created_at: 2015-02-27 21:05:09 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-76471538  

I think check_probabilities should be removed entirely.  The following asserts:  
  
``` c++  
#include <boost/random/hyperexponential_distribution.hpp>  
  
int main() {  
    std::vector<double> weights(1023, 1.0);  
    boost::random::hyperexponential_distribution<> dist(weights, weights);  
}  
```

---

## Comment 7

> Username: swatanabe  
> Created_at: 2015-02-27 21:19:24 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-76473577  

I'd strongly prefer to avoid external links outside the References section.  For links to other boost libraries, you can use boost:/libs/....  For UniformRandomNumberGenerator use \uniform_random_number_generator (This is defined in the doc Jamfile).

---

## Comment 8

> Username: swatanabe  
> Created_at: 2015-02-27 21:28:12 UTC  
> Updated_at: 2015-04-01 09:10:42 UTC  
> Url: https://github.com/boostorg/random/pull/9#discussion_r25540414  

Reading probs_ but not rates_ will leave the object in an invalid state.

---

## Comment 9

> Username: swatanabe  
> Created_at: 2015-02-27 21:29:35 UTC  
> Updated_at: 2015-04-01 09:10:42 UTC  
> Url: https://github.com/boostorg/random/pull/9#discussion_r25540519  

assert is for programming errors.  assert should never be used to validate input.

---

## Comment 10

> Username: sguazt  
> Created_at: 2015-03-09 16:40:57 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-77891332  

Steven,  
  
Thank you for your feedback.  
I've tried to fix some of the issues you've highlighted, specifically:  
- Fixed doc: removed (or moved to a local _References_ section) external links, and replaced internal links as you suggested.  
- Improved the function for reading a `param_type` from an input stream so that the internal state doesn't remain inconsistent (I've also added some tests for this in the `test_streaming` test case).  
- Improved the `check_probabilities` function so that the check against sum of probabilities is some according to the relative magnitude of the two numbers to be checked, that is:  
  `x` is approximately equal to `y` with respect to tolerance `tol`, if and only if `abs(x-y)/max(abs(x),abs(y)) <= tol`.  
  I've found this method in Knuth "The Art of Computer Programming, Vol 3", 3rd Ed, 1998 (Sec. 4.2.2).  
  I've added a test case named `test_normalization`. On my machine, the test passes. But, I admit that the test also passed with the old version of `check_probabilities`. So, before celebrating, I wait for your feedback.  
  
There is still an unresolved issue about the assertions for which I would ask you an opinion.  
Do you suggest,  
1. to replace assertions with exceptions,   
2. to completely remove them,  
3. or what else  
  
?  
  
Thanks,  
  
Marco

---

## Comment 11

> Username: swatanabe  
> Created_at: 2015-03-11 15:06:03 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-78281262  

AMDG  
  
On 03/09/2015 10:40 AM, Marco Guazzone wrote:  
  
> - Improved the function for reading a `param_type` from an input stream so that the internal state doesn't remain inconsistent (I've also added some tests for this in the `test_streaming` test case).  
  
It's still possible for the internal state to  
be inconsistent if is.exceptions has been set.  
  
> - Improved the `check_probabilities` function so that the check against sum of probabilities is some according to the relative magnitude of the two numbers to be checked, that is:  
>   `x` is approximately equal to `y` with respect to tolerance `tol`, if and only if `abs(x-y)/max(abs(x),abs(y)) <= tol`.  
>   I've found this method in Knuth "The Art of Computer Programming, Vol 3", 3rd Ed, 1998 (Sec. 4.2.2).  
>   I've added a test case named `test_normalization`. On my machine, the test passes. But, I admit that the test also passed with the old version of `check_probabilities`. So, before celebrating, I wait for your feedback.  
  
The test that failed for me was something like:  
  
std::vector<double> v(1001, 1.0);  
boost::random::hyperexponential_distribution<> dist(v, v);  
  
I think your new assert will still fire for this.  
In fact, sinply increasing the size of the  
vector is enough to make the assertion fire  
for any constant value of tol.  
  
> There is still an unresolved issue about the assertions for which I would ask you an opinion.  
> Do you suggest,  
> 1. to replace assertions with exceptions,   
> 2. to completely remove them,  
> 3. or what else  
  
You should set the fail bit of the stream.  
  
In Christ,  
Steven Watanabe

---

## Comment 12

> Username: sguazt  
> Created_at: 2015-03-11 21:52:08 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-78380424  

Hi Steven,  
I've changed again  the function `operator>>` for reading the `param_type`. This function now operates in the following manner:  
- If an I/O error occurs (possibly with exception throwing), the state of `parm` (i.e., the object to be read) is left unchanged, and specifically is the same one that `parm` has at the beginning of the function's execution.  
- The state of `parm` is changed only after having completely read the whole input.  
  
Better now?  
  
Concerning, the check for probability normalization, even if the case you've suggested (i.e., `1001` and `1`) on my machine does not cause problem, I agree with you that if it fails on some architecture it is better to remove it.  
So now, `check_probabilities` only checks if each probability is a finite number and is in the range of `[0,1]`.  
Do you agree with these choices?  
  
Best,  
  
Marco

---

## Comment 13

> Username: swatanabe  
> Created_at: 2015-03-11 22:39:46 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-78388387  

AMDG  
  
On 03/11/2015 03:52 PM, Marco Guazzone wrote:  
  
> Hi Steven,  
> I've changed again  the function `operator>>` for reading the `param_type`. This function now operates in the following manner:  
> - If an I/O error occurs (possibly with exception throwing), the state of `parm` (i.e., the object to be read) is left unchanged, and specifically is the same one that `parm` has at the beginning of the function's execution.  
> - The state of `parm` is changed only after having completely read the whole input.  
>   
> Better now?  
  
  That looks good.  My only remaining concern with  
operator>> is how you fix up different sized  
vectors.  I'm not saying that it's wrong, but  
I'd like to hear the justification for why it's  
the right behavior.  In particular:  
- Why is this better than an error?  
- Why fill the smaller range rather than  
  truncating the larger range?  
- Why fill with 1 rather than some other  
  value (say 0)?  
    
  If we agree that this kind of fixing up is  
  the right thing to do, then I'll need to  
  make the piecewise_constant and piecewise_linear  
  distributions match.  
  
> Concerning, the check for probability normalization, even if the case you've suggested (i.e., `1001` and `1`) on my machine does not cause problem, I agree with you that if it fails on some architecture it is better to remove it.  
> So now, `check_probabilities` only checks if each probability is a finite number and is in the range of `[0,1]`.  
> Do you agree with these choices?  
  
I'm surprised that you can't duplicate this.  I'm not exactly  
using an unusual architecture.  Here's the complete test program:  
  
#include <boost/random/hyperexponential_distribution.hpp>  
#include <vector>  
  
int main() {  
    std::vector<double> v(1001, 1.0);  
    boost::random::hyperexponential_distribution<> d(v, v);  
}  
  
It fails with an assertion with MSVC 2013 and gcc 4.8.3.  
  
In Christ,  
Steven Watanabe

---

## Comment 14

> Username: swatanabe  
> Created_at: 2015-03-11 23:22:52 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-78394545  

AMDG  
  
On 03/09/2015 10:40 AM, Marco Guazzone wrote:  
  
> - Fixed doc: removed (or moved to a local _References_ section) external links, and replaced internal links as you suggested.  
  
I'd rather not refer to cppreference.com at all.  If you're  
going to reference anything, the official source is:  
C++11 (ISO/IEC 14882-2011)  
8.5.4 [dcl.init.list] and  
24.2.3 [input.iterators]  
  
In Christ,  
Steven Watanabe

---

## Comment 15

> Username: sguazt  
> Created_at: 2015-03-12 11:35:45 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-78464187  

Hi Steven,  
  
> My only remaining concern with operator>> is how you fix up different sized vectors. I'm not saying  
> that it's wrong, but I'd like to hear the justification for why it's the right behavior. In particular:  
> - Why is this better than an error?  
  
In Boost.Random, I saw very few places where exceptions are thrown (i.e., in `seed_impl.hpp` and `random_device.cpp`). So I thought that the policy in Boost.Random is to avoid throwing exception.  
However, if we are allowed to throw exceptions, then I agree with you that it is a better choice than the current kind of _self-healing_ behavior.  
In this case, I would opt for `std::runtime_error`, would you?  
  
> - Why fill the smaller range rather than truncating the larger range?  
  
No particular reason. This is an arbitrary choice.  
  
> - Why fill with 1 rather than some other value (say 0)?  
  
Hmmm. Indeed, for probabilities `0` may be a good choice if we want that the corresponding exponential distribution will never be selected. On the other hand, to make sure the corresponding exponential distribution is selected at some point in the future, we need to set a value `> 0`. Again, my choice is totally arbitrary.  
By the way, I've found that setting probabilities to `1` without subsequently calling the normalization function is a bug (thank you for pointing it out). So, whatever value we choose for probabilities (except for `0`), the correct way is to call the normalization function later (e.g., at line 517). Before fixing it, I see what we decide about throwing exceptions.  
Concerning rates, `0` is not an admissible value (rates must be `> 0`). So `1` is just a matter of choice for setting rates to a valid value.  
  
Best,  
  
Marco

---

## Comment 16

> Username: sguazt  
> Created_at: 2015-03-12 11:36:26 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-78464262  

> I'm surprised that you can't duplicate this. I'm not exactly using an unusual architecture.  
  
Don't know why the test runs fine on my machine and does not on yours.  
To test this issue I wrote the test case `test_normalization` in `test_hyperexponential_distribution.cpp`.  Now, I've also added in the same test an explicit call to `check_params` (which in turn calls `check_probabilities`), in case `assert`s were not enabled by the compiler. As you can see, the test case checks against the two cases you've reported above.  
On my machine it passed without problems.  
My system is:  
- Intel(R) Core(TM) i7-3540M  
- Fedora Linux 21 x86_64  
- GCC 4.9.2 20150212 (Red Hat 4.9.2-6)  
  
Note (just to clarify), in the current commit, the test case and the offending check in `check_probabilities` are still commented. So, in the case you want to run such test case, you need to uncomment them:  
- lines 242:261 in `test_hyperexponential_distribution.cpp`  
- lines 95:100 in `hyperexponential_distribution.hpp`  
  
Best,  
  
Marco

---

## Comment 17

> Username: sguazt  
> Created_at: 2015-03-12 11:37:06 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-78464340  

> I'd rather not refer to cppreference.com at all. If you're going to reference anything, the official  
> source is: C++11 (ISO/IEC 14882-2011) 8.5.4 [dcl.init.list] and 24.2.3 [input.iterators]  
  
Sure, definitely better.  
I've fixed it  
  
Thank you  
  
Marco

---

## Comment 18

> Username: swatanabe  
> Created_at: 2015-03-12 17:16:51 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-78533959  

AMDG  
  
On 03/12/2015 05:35 AM, Marco Guazzone wrote:  
  
> > My only remaining concern with operator>> is how you fix up different sized vectors. I'm not saying  
> > that it's wrong, but I'd like to hear the justification for why it's the right behavior. In particular:  
> > - Why is this better than an error?  
>   
> In Boost.Random, I saw very few places where exceptions are thrown (i.e., in `seed_impl.hpp` and `random_device.cpp`). So I thought that the policy in Boost.Random is to avoid throwing exception.  
> However, if we are allowed to throw exceptions, then I agree with you that it is a better choice than the current kind of _self-healing_ behavior.  
> In this case, I would opt for `std::runtime_error`, would you?  
  
The convention for stream operators is to use  
is.setstate(std::ios_base::failbit);  
  
This may throw an exception if the stream  
has been configured to throw.  
  
> > - Why fill the smaller range rather than truncating the larger range?  
>   
> No particular reason. This is an arbitrary choice.  
>   
> > - Why fill with 1 rather than some other value (say 0)?  
>   
> Hmmm. Indeed, for probabilities `0` may be a good choice if we want that the corresponding exponential distribution will never be selected. On the other hand, to make sure the corresponding exponential distribution is selected at some point in the future, we need to set a value `> 0`. Again, my choice is totally arbitrary.  
> By the way, I've found that setting probabilities to `1` without subsequently calling the normalization function is a bug (thank you for pointing it out). So, whatever value we choose for probabilities (except for `0`), the correct way is to call the normalization function later (e.g., at line 517). Before fixing it, I see what we decide about throwing exceptions.  
> Concerning rates, `0` is not an admissible value (rates must be `> 0`). So `1` is just a matter of choice for setting rates to a valid value.  
  
Okay.  '0' also won't work for probabilities if the  
given probability vector is empty.  If we can't use  
'0', then '1' is probably the best choice.  
  
In Christ,  
Steven Watanabe

---

## Comment 19

> Username: sguazt  
> Created_at: 2015-03-13 08:39:28 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-78869078  

Hi,  
  
Now, in case of nonconformant vectors' size, the `failbit` state is set.  
Also, I've expanded the `test_streaming` test case to cover tests (1) when `exceptions(failbit)` is either disabled or enabled, and (2) to test the reading of both `param_type` and `hyperexponential_distribution` object.  
  
Best,  
  
Marco

---

## Comment 20

> Username: sguazt  
> Created_at: 2015-03-22 08:50:08 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-84562555  

Hi Steven,  
  
Have you had time to check my last changes?  
Any other change in order to finally get hyper-exponential merged into the main branch?  
  
Thank you so much.  
  
Best,  
  
Marco

---

## Comment 21

> Username: swatanabe  
> Created_at: 2015-03-28 18:36:26 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-87281297  

AMDG  
  
On 03/22/2015 02:50 AM, Marco Guazzone wrote:  
  
> Have you had time to check my last changes?  
> Any other change in order to finally get hyper-exponential merged into the main branch?  
  
I'm getting errors with C++11:  
test_hyperexponential_distribution.cpp(68) : error C3861: 'list_of':   
identifier not found  
  
Also, on the iterator constructor:  
// Note that we allow different argument types here to allow for  
//  construction from an array plus a pointer into that array.  
hyperexponential_distribution(RateIterT const& rate_first,  
                               RateIterT2 const& rate_last);  
  
This doesn't actually work, because you can end up with  
  
RateIterT = double[N]  
RateIterT2 = double *  
  
rate_first then decays to const double *, which  
makes the call to std::distance ambiguous.  
  
In Christ,  
Steven Watanabe

---

## Comment 22

> Username: sguazt  
> Created_at: 2015-03-29 13:28:01 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-87413230  

Thank you Steven.  
  
> I'm getting errors with C++11: test_hyperexponential_distribution.cpp(68) : error C3861: 'list_of': identifier not found   
  
Indeed, some of the `list_of` occurrences were without `boost::assign` qualifier.  
I've corrected the code, but I was not able to test it. How do you tell `bjam` to compile with C++11?  
  
> This doesn't actually work, because you can end up with  
> RateIterT = double[N]  
> RateIterT2 = double \* rate_first  
> then decays to const double *, which makes the call to std::distance ambiguous.  
  
Yes, you're right. I've fixed it by removing the `const&` and the second iterator type `RateIterT2`.  
Also, I've added a related test case in `test_hyperexponential_distribution.cpp` (see lines `99` to `106`).  
  
Cheers,  
  
Marco

---

## Comment 23

> Username: swatanabe  
> Created_at: 2015-03-30 16:50:03 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-87747362  

AMDG  
  
On 03/29/2015 07:28 AM, Marco Guazzone wrote:  
  
> Thank you Steven.  
>   
> > I'm getting errors with C++11: test_hyperexponential_distribution.cpp(68) : error C3861: 'list_of': identifier not found   
>   
> Indeed, some of the `list_of` occurrences were without `boost::assign` qualifier.  
> I've corrected the code, but I was not able to test it. How do you tell `bjam` to compile with C++11?  
  
With gcc, cxxflags=-std=c++11  
  
In Christ,  
Steven Watanabe

---

## Comment 24

> Username: sguazt  
> Created_at: 2015-03-31 10:05:43 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-88023698  

Thank you.  
  
I've fixed another bug in a test case.  
Now all tests compile even with C++11.  
  
Cheers,  
  
Marco

---

## Comment 25

> Username: swatanabe  
> Created_at: 2015-03-31 22:12:20 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-88268494  

AMDG  
  
The only remaining problems are with Doxygen:  
- parm is #defined to param by the doxyfile,  
  which results in messages like  
  boost/random/hyperexponential_distribution.hpp:688: warning: argument  
  'parm' of command @param is not found in the argument list of  
  boost::random::hyperexponential_distribution< RealT  
    
  > ::hyperexponential_distribution(param_type const &param)  
- Doxygen is generating <ndash/> which is not correctly  
  translated by BoostBook/DocBook.  I'll fix this one in  
  doxygen2boostbook.xsl.  
  
In Christ,  
Steven Watanabe

---

## Comment 26

> Username: sguazt  
> Created_at: 2015-04-01 09:14:30 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-88408133  

Hi Steven,  
  
I think to have fixed the above issues:  
1. I've renamed `parm` into `param`  
2. I've changed `--` into `-` in bibliographic references  
  
Now, `bjam doc` stops to complain on my machine.  
Let me know if there are other problems  
  
Cheers,  
  
Marco

---

## Comment 27

> Username: sguazt  
> Created_at: 2015-04-03 07:39:02 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-89206525  

Thank you, Steven.  
  
Just to know... You closed this pull request without merging.  
Do you plan to merge soon, or do you think this is not ready to be merged yet?  
  
Cheers,  
  
Marco

---

## Comment 28

> Username: swatanabe  
> Created_at: 2015-04-03 14:40:34 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-89306749  

AMDG  
  
On 04/03/2015 01:39 AM, Marco Guazzone wrote:  
  
> Just to know... You closed this pull request without merging.  
> Do you plan to merge soon, or do you think this is not ready to be merged yet?  
  
The changes are in develop.  I squashed them.  
  
In Christ,  
Steven Watanabe

---

## Comment 29

> Username: sguazt  
> Created_at: 2015-04-03 14:47:59 UTC  
> Url: https://github.com/boostorg/random/pull/9#issuecomment-89309250  

Thanks!  
  
Marco

---
