# #23 fix issue resolving pow correctly, updated docs [Closed]

> Username: jeking3  
> Created at: 2018-07-13 15:05:46 UTC  
> Updated at: 2018-07-13 20:26:30 UTC  
> Closed at: 2018-07-13 20:26:30 UTC  
> Url: https://github.com/boostorg/rational/pull/23  



---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-07-13 15:47:28 UTC  
> Url: https://github.com/boostorg/rational/pull/23#issuecomment-404872959  

AMDG  
  
- It might be better to check std::numeric_limits<T>::is_integer  
  instead of std::is_integral, as this allows user-defined types.  
  I believe this is more consistent with the existing code (see  
  rational_detail::is_compatible_integer).  
- I'm not sure what test/expected_fail_12.cpp is  
  supposed to be testing.  
- in rational_pow_test, using boost::pow is  
  unnecessary as it will always be found by ADL.  
- no_std_pow_collision is useless because  
  std::pow(float, float) is an exact match.  
- Please verify that the new tests fail for  
  the old code.  If they already pass for the  
  old code, then they're not very useful.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-07-13 15:52:11 UTC  
> Url: https://github.com/boostorg/rational/pull/23#issuecomment-404874324  

If you look at https://github.com/qtumproject/cpp-eth-qtum/issues/31#issuecomment-404867094 it shows the original template was properly omitted, so the issue is something else and this change may not even be necessary.  I suspect at this point that it is a failure to include <cmath> in the proper location (perhaps inside boost::spirit where it is being used?) or the C++ language level changed and caused that invocation of pow to no longer match, since C++11 removed some pow() signatures (in favor of what I would guess is a template that requires the same type for the base and the exponent).

---

## Comment 3

> Username: swatanabe  
> Created_at: 2018-07-13 15:53:34 UTC  
> Url: https://github.com/boostorg/rational/pull/23#issuecomment-404874702  

AMDG  
  
Also, I wouldn't classify pow as a utility function.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: swatanabe  
> Created_at: 2018-07-13 16:20:11 UTC  
> Url: https://github.com/boostorg/rational/pull/23#issuecomment-404882070  

AMDG  
  
On 07/13/2018 09:52 AM, James E. King III wrote:  
> If you look at https://github.com/qtumproject/cpp-eth-qtum/issues/31#issuecomment-404867094 it shows the original template was properly omitted, so the issue is something else and this change may not even be necessary.  
  
Does commenting out the definition of pow for rational  
fix the problem?  
  
>  I suspect at this point that it is a failure to include <cmath> in the proper location (perhaps inside boost::spirit where it is being used?)  
  
cmath is #included right at the top of the spirit header.  
  
> or the C++ language level changed and caused that invocation of pow to no longer match, since C++11 removed some pow() signatures (in favor of what I would guess is a template that requires the same type for the base and the exponent).  
>   
  
pow(double, double) is definitely defined in all versions  
of the standard.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: swatanabe  
> Created_at: 2018-07-13 16:43:53 UTC  
> Url: https://github.com/boostorg/rational/pull/23#issuecomment-404888368  

AMDG  
  
On 07/13/2018 09:52 AM, James E. King III wrote:  
> If you look at https://github.com/qtumproject/cpp-eth-qtum/issues/31#issuecomment-404867094 it shows the original template was properly omitted, so the issue is something else and this change may not even be necessary.  I suspect at this point that it is a failure to include <cmath> in the proper location (perhaps inside boost::spirit where it is being used?) or the C++ language level changed and caused that invocation of pow to no longer match, since C++11 removed some pow() signatures (in favor of what I would guess is a template that requires the same type for the base and the exponent).  
>   
  
Ugh.  boost::pow is hiding std::pow.  Here's why:  
  
- "name lookup ends as soon as a declaration is found for the  
  name." [basic.lookup.unqual]  
- For using directives: "During unqualified name lookup (3.4.1),  
  the names appear as if they were declared in the nearest  
  enclosing namespace which contains both the using-directive  
  and the nominated namespace." [namespace.udir] (i.e. using  
  std::pow effectively puts pow in the global namespace)  
  
Note that this happens before overload resolution and  
is not affected by the fact that boost::pow does not  
match.  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-07-13 16:51:06 UTC  
> Url: https://github.com/boostorg/rational/pull/23#issuecomment-404890178  

Perhaps we should move pow into rational (and make it a static?), so that one needs to use boost::rational::pow(...) to use it?

---

## Comment 7

> Username: jeking3  
> Created_at: 2018-07-13 17:21:43 UTC  
> Url: https://github.com/boostorg/rational/pull/23#issuecomment-404898337  

Or it could change to be `operator ^` and we could also add `operator ^=`  to the class.

---

## Comment 8

> Username: jeking3  
> Created_at: 2018-07-13 18:25:11 UTC  
> Url: https://github.com/boostorg/rational/pull/23#issuecomment-404914881  

I'm starting to think it would be better to just remove the change from 1.68 and possibly deal with it later.  This implementation is incomplete - it won't work on rational exponents, for example, and the way it was originally implemented in the PR from 2015 is flawed due to the name collision already experienced by one consumer.  Granted in Boost.Spirit, instead of "using" simply putting in "std::pow" would be more explicit however I'm concerned about any other implementations that might end up running into the same problem.  In this case I believe the issue is limited to implementations within the boost namespace, based on what Steven diagnosed.  `boost::pow` just seems like a bad idea at the moment given we're going to release 1.68.0 soon and we're in beta.  Thoughts?

---

## Comment 9

> Username: swatanabe  
> Created_at: 2018-07-13 18:39:55 UTC  
> Url: https://github.com/boostorg/rational/pull/23#issuecomment-404918612  

AMDG  
  
On 07/13/2018 12:25 PM, James E. King III wrote:  
> I'm starting to think it would be better to just remove the change from 1.68 and possibly deal with it later.  
  
+1  
  
>  This implementation is incomplete - it won't work on rational exponents, for example,  
  
rational exponents should not be supported as the result is  
not rational.  
  
> and the way it was originally implemented in the PR from 2015 is flawed due to the name collision already experienced by one consumer.  Granted in Boost.Spirit, instead of "using" simply putting in "std::pow" would be more explicit  
  
We still need ADL, so `using std::pow` is the right way, I think.  
  
> however I'm concerned about any other implementations that might end up running into the same problem.    
  
I'm certain that there are.  
  
> In this case I believe the issue is limited to implementations within the boost namespace, based on what Steven diagnosed.  `boost::pow` just seems like a bad idea at the moment given we're going to release 1.68.0 soon and we're in beta.  Thoughts?  
>   
  
Right.  We'd need to check all of boost and there's no  
way something at that scale will fly, this late in the  
release process.  
  
In Christ,  
Steven Watanabe

---
