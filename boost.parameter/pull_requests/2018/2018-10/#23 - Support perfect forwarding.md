# #23 Support perfect forwarding [Merged]

> Username: CromwellEnage  
> Created at: 2018-10-29 14:13:47 UTC  
> Updated at: 2018-11-12 13:16:42 UTC  
> Merged at: 2018-11-07 13:21:56 UTC  
> Closed at: 2018-11-07 13:21:56 UTC  
> Url: https://github.com/boostorg/parameter/pull/23  

1. The end of section 3.2.1 of the current home page tutorial notes "that because of the forwarding problem, parameter::parameters::operator() can't accept non-const rvalues."  Add code to eliminate this problem.  As a positive side effect, Boost.Parameter-enabled functions and constructors are no longer bound by BOOST_PARAMETER_MAX_ARITY on compilers that support perfect forwarding. User code can now check for this support by detecting the configuration macro BOOST_PARAMETER_HAS_PERFECT_FORWARDING, or manually turn off this support by defining the configuration macro BOOST_PARAMETER_DISABLE_PERFECT_FORWARDING.  See "test/evaluate_category.cpp" and "test/preprocessor_eval_category.cpp" for example usage.  
  
2. Add parameter category qualifiers "consume" and "move_from"(current qualifiers are "in", "out", "in_out", and "forward") based on http://www.modernescpp.com/index.php/c-core-guidelines-how-to-pass-function-parameters.  
  
3. Update documentation to reflect the above changes.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-10-29 14:32:58 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-433932543  

Since perfect forwarding requires C++11 we now have a C++11 library, which is fine since Boost is no longer going to "support" C++03, although libraries which still do support C++03 are still allowed in Boost as long as they correctly build for later versions of the C++ standard. Therefore I do not think any effort should be made in Parameter to turn off perfect forwarding as there is no benefit to doing so since we are saying that Parameter is C++11, and it just adds an unnecessary complication to the code. In other words let's embrace the fact that Parameter will be C++11 on up without trying to maintain backward compatibility with C++03.

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-10-29 14:52:07 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-433940104  

My error. I did not look to see what you meant by "perfect forwarding" so I did not realize that you meant using the forwarding solution in Boost.Move.

---

## Comment 3

> Username: eldiener  
> Created_at: 2018-10-29 15:11:04 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-433947763  

Then again, since you have added rvalue references, among other C++11 features, my first comment still holds; there is no reason not to use C++11 libraries and constructs, such as std::forward, unless you have a particular reason for choosing the Boost version over the standard version. This is just my opinion, regarding Parameter, so feel free to disagree. But Boost has said that supporting C++03 users is not going to happen as Boost moves forward starting with the upcoming release so using C++11 on up, even for a library like Parameter which has supported C++03, is no longer necessary.

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-10-29 15:58:40 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-433967149  

140 travis jobs is a bit too much.

---

## Comment 5

> Username: eldiener  
> Created_at: 2018-10-29 16:23:06 UTC  
> Updated_at: 2018-10-29 16:23:41 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-433976728  

We don't need to test c++03 anymore. My own personal opinion is that we should not be testing gcc before 4.8 and clang before 3.8, but I can see from other travis CI tests that others disagree. I just can't imagine any people realistically using really old releases when both gcc and clang have provided numerous more modern releases and both are completely free compilers; gcc is up to 8.1 and clang is up to 7.0 in officially releases.

---

## Comment 6

> Username: CromwellEnage  
> Created_at: 2018-10-29 16:41:03 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-433983944  

Okay, my next commit will remove C++03 support and remove the corresponding compiler configurations from the test matrices.  This should also cut down on job times drastically.

---

## Comment 7

> Username: pdimov  
> Created_at: 2018-10-29 16:43:28 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-433984883  

I don't think that the problem here is caused by testing C++03. It's more a function of multiplying all the jobs by 8. This style has probably been copied from Math or Multiprecision, and they really don't need to be taken as a shining example of best Travis practices.

---

## Comment 8

> Username: CromwellEnage  
> Created_at: 2018-10-29 16:44:35 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-433985332  

Gotcha.  
  
One more question, though: what should I do with BOOST_PARAMETER_MAX_ARITY now that perfect forwarding makes it unnecessary?

---

## Comment 9

> Username: pdimov  
> Created_at: 2018-10-29 16:53:38 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-433989150  

The straightforward answer is to ignore it, but if you're asking the question, there might be subtleties that I'm missing.

---

## Comment 10

> Username: CromwellEnage  
> Created_at: 2018-10-29 18:47:50 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-434033690  

Okay, I'll deprecate it but leave it defined in case any user code is out there using it.

---

## Comment 11

> Username: eldiener  
> Created_at: 2018-10-29 18:53:29 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-434035588  

I agree with Peter. You can keep BOOST_PARAMETER_MAX_ARITY as it is in the code, but just ignore it. Also document that it is being kept so as not to break anyone's compile but that because of perfect forwarding it is deprecated and just ignored. Then maybe after a release with your changes you can remove it entirely from the source code.

---

## Comment 12

> Username: CromwellEnage  
> Created_at: 2018-10-30 13:02:33 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-434292112  

It turns out that I can't deprecate BOOST_PARAMETER_MAX_ARITY just yet.  I relaxed the perfect forwarding requirements by allowing boost::mpl::vector to be the underlying sequence used by boost::parameter::parameters if neither boost::fusion::list nor boost::fusion::deque are true variadic sequences.  However, unlike those two, boost::mpl::vector has a size limit, so I have to set BOOST_PARAMETER_MAX_ARITY to that limit just in case.

---

## Comment 13

> Username: pdimov  
> Created_at: 2018-10-30 14:43:43 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-434329056  

If we're going to drop C++03 (about which I'm not convinced), you might as well switch to mp11. :-)

---

## Comment 14

> Username: eldiener  
> Created_at: 2018-10-30 15:40:33 UTC  
> Updated_at: 2018-10-30 15:41:02 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-434352360  

Sure mp11 can be used, but maybe one step at a time is best. Since the current changes deal with rvalue references we are already in C++11 mode so I don't see any reason to still try to support C++03 and complicate code unnecessarily. As far as keeping BOOST_PARAMETER_MAX_ARITY for now I think that is fine. I am going to merge this once the checks have completed and if Cromwell wants to look at mp11 he can but I want to go ahead and get his current design changes into Parameter first.

---

## Comment 15

> Username: pdimov  
> Created_at: 2018-10-30 15:52:45 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-434357479  

I'm not sure I understand what we're talking about here. Are these perfect forwarding changes conditional on C++11, or are we talking about dropping support for C++03 in Parameter entirely?  
  
If the latter, that's going to affect a number of libraries.  
  
https://pdimov.github.io/boostdep-report/develop/parameter.html#reverse-dependencies

---

## Comment 16

> Username: eldiener  
> Created_at: 2018-10-30 21:33:01 UTC  
> Updated_at: 2018-11-02 23:52:37 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-434478059  

I can understand that making Parameter C++11 means making all other Boost libraries which use Parameter C++11 libraries. Nonetheless I would like to merge this PR and then maybe have a discussion about Parameter on the developer's mailing list. Personally, since we had a long discussion about dropping C++03 support in Boost it does seem like we should go in that direction for Parameter also, even if it means we are doing so for other Boost libraries. Since I offered cxxd, and it was rejected as a solution to the C++03/C++11 solution, it seems to me that the consensus is that a programmer should either support C++03 or support C++11 on up, but not waste time attempting to support both with a hybrid solution. Of course this is just my take, but I think that manual efforts to support a hybrid solution are just not worthwhile.

---

## Comment 17

> Username: CromwellEnage  
> Created_at: 2018-11-01 02:36:11 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-434911418  

Hi, Peter.  
  
Short of reinstating C++03 support (which I am also willing to do, once I figure out how to cut down on jobs and job times on my local copy), what would allay your concerns that user code of the libraries using Boost.Parameter in turn won't break?

---

## Comment 18

> Username: eldiener  
> Created_at: 2018-11-02 23:58:04 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-435541889  

If the previous public/protected interface has not changed function signatures, then libraries using Parameter should not break.

---

## Comment 19

> Username: eldiener  
> Created_at: 2018-11-03 00:04:31 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-435542765  

Can you bring up yourself a discusssion about your changes which make Parameter a C++11 library on the Boost developer's mailing list, versus Peter's list of dependencies theoretically making all the libraries which use Boost C++11 libraries ? I do no think we want to do that for a library like Boost Config, which is used by nearly all other Boost libraries, but I have a strong suspicion that many, if not most of the libraries in Peter's list do not really use portions of Parameter which will have C++11 constructs. It may be possible to change a number of libraries which depend on Parameter to use specific headers which do not bring in any C++11 constructs with your proposed  changes, therefore keeping them C++03 libraries even if they use Parameter.

---

## Comment 20

> Username: pdimov  
> Created_at: 2018-11-03 00:39:44 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-435546898  

Right, I don't really have any concerns of my own here, since I neither maintain nor use any of those libraries, I'm just the messenger. The list is the appropriate place to bring this up.

---

## Comment 21

> Username: CromwellEnage  
> Created_at: 2018-11-03 02:18:22 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-435553121  

I've started the discussion on the mailing list.  Let us see what they say.

---

## Comment 22

> Username: eldiener  
> Created_at: 2018-11-03 04:24:52 UTC  
> Url: https://github.com/boostorg/parameter/pull/23#issuecomment-435559606  

I erroneously mentioned Boost Config above. Please ignore that reference. The libraries dependent on Parameter were correctly mentioned in the Boost mailing list discussion which Cromwell Enage started.

---
