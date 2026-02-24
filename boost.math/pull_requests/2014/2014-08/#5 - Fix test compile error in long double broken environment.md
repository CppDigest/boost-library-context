# #5 Fix test compile error in long double broken environment. [Merged]

> Username: Flast  
> Created at: 2014-08-16 15:55:15 UTC  
> Updated at: 2014-08-20 01:29:55 UTC  
> Merged at: 2014-08-19 16:51:34 UTC  
> Closed at: 2014-08-19 16:51:34 UTC  
> Url: https://github.com/boostorg/math/pull/5  

This PR will fix following compile error.  
  
[test_real_concept](http://www.boost.org/development/tests/develop/developer/output/FBSD-64%20jc-bell-boost-bin-v2-libs-math-test-test_real_concept-test-gcc-4-2-1-debug-link-static.html)  
[test_constants](http://www.boost.org/development/tests/develop/developer/output/FBSD-64%20jc-bell-boost-bin-v2-libs-math-test-test_constants-test-gcc-4-2-1-debug-link-static.html)  
[cstdfloat_concept_check_2](http://www.boost.org/development/tests/develop/developer/output/FBSD-64%20jc-bell-boost-bin-v2-libs-math-test-cstdfloat_concept_check_2-test-gcc-4-2-1-debug-link-static.html)

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2014-08-16 20:20:13 UTC  
> Url: https://github.com/boostorg/math/pull/5#issuecomment-52404714  

Thank you for your report and the suggested fixes. We are looking into it. Cheers, Chris.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2014-08-17 08:33:21 UTC  
> Url: https://github.com/boostorg/math/pull/5#issuecomment-52416582  

I've fixed the missing #include in https://github.com/boostorg/math/commit/60441c5d3a5d59f61e68deb2fddd06dee3561a75.  
And in https://github.com/boostorg/math/commit/3fe188da326546bbd48f9acef433d20870b663c1 I've removed all references to  pow23_four_minus_pi as it's no longer in the source.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2014-08-17 08:38:17 UTC  
> Url: https://github.com/boostorg/math/pull/5#issuecomment-52416662  

That just leaves the cstdfloat fix - I see two solutions here:  
  
1) As the OP proposes, don't define any cstdfloat types to be a long double if full support for that type is not present.  
2) Make use of long double in cstdfloat even if full std lib support isn't present, but disable full concept checking of types > 64 bits when BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS is set.  
  
The second option gives users a sporting chance of making some use of the exended types as long as they don't want full std lib support.  On the other hand some code expecting cstdfloat types to give portable behaviour may be broken.  
  
Chris, what do you think is the more useful?

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2014-08-17 09:04:07 UTC  
> Url: https://github.com/boostorg/math/pull/5#issuecomment-52417062  

> That just leaves the cstdfloat fix - I see two solutions here:  
  
Thank you for the other fixes, John.  
  
> 1) As the OP proposes, don't define any cstdfloat types to be  
> a long double if full support for that type is not present.  
>   
> 2) Make use of long double in cstdfloat even if full std lib support  
> isn't present, but disable full concept checking of types > 64 bits  
> when BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS is set.  
> The second option gives users a   
> sporting chance of making some use  
> of the exended types as long as they   
> don't want full std lib support.  
> On the other hand some code expecting   
> cstdfloat types to give portable  
> behaviour may be broken.  
>   
> Chris, what do you think is the more useful?  
  
My lazy side says, "OK, let's just go for 1) here. It is clean.  
It solves the OP. And it is consistent in the sense that  
it does not bring in only part of the solution for long double.  
  
In 2) there is a slight consistency problem. Let's say we  
pull in the types only for long double. But users can not fully  
use them within the context of the C++ std lib with normal  
functions. In other words, even simple things such as  
"y = std::sin(boost::float80_t x)" do not work. Then we have  
a solution for long double that is only half right. Or is it  
rather half wrong?  
  
I wonder if there is a 2a) via making use of ::fabsl, sinl, etc.?  
  
When BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS  
is set, what does this mean? Does it mean that there are no  
long double functions in the std namespace but there are  
long double C-style functions in the global namespace such  
as fabsl(), sinl() ? Or does it mean that there are no  
long double functions whatsoever?  
  
If there are no long double functions whatsoever, then 1)  
might be the way to go because it is consistent. But if  
there are no long double functions in std namespace,  
but there are C-style long double functions in global,  
then we could wrap them as we did for boost::float128_t.  
  
I don't have any compilers without long double support.  
So I don't have any test case. What does  
BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS  
really mean?  
  
Cheers, Chris.  
  
On Sunday, August 17, 2014 10:38 AM, jzmaddock notifications@github.com wrote:  
  
That just leaves the cstdfloat fix - I see two solutions here:  
1) As the OP proposes, don't define any cstdfloat types to be a long double if full support for that type is not present.  
2) Make use of long double in cstdfloat even if full std lib support isn't present, but disable full concept checking of types > 64 bits when BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS is set.  
The second option gives users a sporting chance of making some use of the exended types as long as they don't want full std lib support.  On the other hand some code expecting cstdfloat types to give portable behaviour may be broken.  
Chris, what do you think is the more useful?  
—  
Reply to this email directly or view it on GitHub.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2014-08-17 12:05:36 UTC  
> Url: https://github.com/boostorg/math/pull/5#issuecomment-52420151  

> My lazy side says, "OK, let's just go for 1) here. It is clean.  
> It solves the OP. And it is consistent in the sense that  
> it does not bring in only part of the solution for long double.  
>   
> In 2) there is a slight consistency problem. Let's say we  
> pull in the types only for long double. But users can not fully  
> use them within the context of the C++ std lib with normal  
> functions. In other words, even simple things such as  
> "y = std::sin(boost::float80_t x)" do not work. Then we have  
> a solution for long double that is only half right. Or is it  
> rather half wrong?  
>   
> I wonder if there is a 2a) via making use of ::fabsl, sinl, etc.?  
>   
> When BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS  
> is set, what does this mean? Does it mean that there are no  
> long double functions in the std namespace but there are  
> long double C-style functions in the global namespace such  
> as fabsl(), sinl() ? Or does it mean that there are no  
> long double functions whatsoever?  
>   
> If there are no long double functions whatsoever, then 1)  
> might be the way to go because it is consistent. But if  
> there are no long double functions in std namespace,  
> but there are C-style long double functions in global,  
> then we could wrap them as we did for boost::float128_t.  
>   
> I don't have any compilers without long double support.  
> So I don't have any test case. What does  
> BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS  
> really mean?  
  
Good question, it depends ;-)  
  
Generally GCC's libstdc++ will define std::sin(long double) etc regardless.  
  
I think in the past I've seen systems that crash if you call the long   
double overloads, others than generate linker errors ('cos there's no   
sinl etc), but the most common - and my "favourite" are platforms where   
things like:  
  
sqrt(finite_long_double_value)  
  
returns +INF because the call is silently forwarded to sqrt(double).   
Similar issues have been spotted with floor, ceil, and even fabs/abs,   
sometimes on systems like the Cray compiler where there's perfectly good   
long double support, but the long double overloads in namespace std are   
buggy (that one should get fixed at some point though).  All of these   
cause hard to track down and impossible to reason about bugs in our code   
because you can never predict when a std lib function might suddenly   
decide to return garbage.  
  
There's also an ever diminishing number of platforms where long double   
is a "double double" with 106 _non-contiguous_ bits where epsilon is   
10^-308 and the type is genuinely impossible to reason about in any   
sensible way.  Thankfully that abomination could never be used for a   
cstdfloat type anyway.  
  
Not sure that helps much, John.

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2014-08-17 19:14:47 UTC  
> Url: https://github.com/boostorg/math/pull/5#issuecomment-52431646  

> > > That just leaves the cstdfloat fix - I see two solutions here:  
> > > 1) As the OP proposes, don't define any cstdfloat types  
> > > to be a long double if full support for that type is not present  
  
<snip>  
  
> > I don't have any compilers without long double support.  
> > So I don't have any test case. What does  
> > BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS  
> > really mean?  
>   
> Good question, it depends ;-)  
  
<snip : long discussion about many flavors of long double>  
  
Well, in light of all those flavors of long double and the  
inherent nature or errors tending to creep up, I would  
vote for 1). In other words, when long double is not double and  
BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS  
is defined, supply the types, but remove the use of std lib  
functions using long double.  
  
But I will let you make thefinal decision. I can comfortably  
go with 1) or 2).  
  
Cheers, Chris.  
  
On Sunday, August 17, 2014 2:05 PM, jzmaddock notifications@github.com wrote:  
  
> My lazy side says, "OK, let's just go for 1) here. It is clean.  
> It solves the OP. And it is consistent in the sense that  
> it does not bring in only part of the solution for long double.  
>   
> In 2) there is a slight consistency problem. Let's say we  
> pull in the types only for long double. But users can not fully  
> use them within the context of the C++ std lib with normal  
> functions. In other words, even simple things such as  
> "y = std::sin(boost::float80_t x)" do not work. Then we have  
> a solution for long double that is only half right. Or is it  
> rather half wrong?  
>   
> I wonder if there is a 2a) via making use of ::fabsl, sinl, etc.?  
>   
> When BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS  
> is set, what does this mean? Does it mean that there are no  
> long double functions in the std namespace but there are  
> long double C-style functions in the global namespace such  
> as fabsl(), sinl() ? Or does it mean that there are no  
> long double functions whatsoever?  
>   
> If there are no long double functions whatsoever, then 1)  
> might be the way to go because it is consistent. But if  
> there are no long double functions in std namespace,  
> but there are C-style long double functions in global,  
> then we could wrap them as we did for boost::float128_t.  
>   
> I don't have any compilers without long double support.  
> So I don't have any test case. What does  
> BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS  
> really mean?  
  
Good question, it depends ;-)  
  
Generally GCC's libstdc++ will define std::sin(long double) etc regardless.  
  
I think in the past I've seen systems that crash if you call the long   
double overloads, others than generate linker errors ('cos there's no   
sinl etc), but the most common - and my "favourite" are platforms where   
things like:  
  
sqrt(finite_long_double_value)  
  
returns +INF because the call is silently forwarded to sqrt(double).   
Similar issues have been spotted with floor, ceil, and even fabs/abs,   
sometimes on systems like the Cray compiler where there's perfectly good   
long double support, but the long double overloads in namespace std are   
buggy (that one should get fixed at some point though).  All of these   
cause hard to track down and impossible to reason about bugs in our code   
because you can never predict when a std lib function might suddenly   
decide to return garbage.  
  
There's also an ever diminishing number of platforms where long double   
is a "double double" with 106 _non-contiguous_ bits where epsilon is   
10^-308 and the type is genuinely impossible to reason about in any   
sensible way.  Thankfully that abomination could never be used for a   
cstdfloat type anyway.  
  
Not sure that helps much, John.   
—  
Reply to this email directly or view it on GitHub.

---

## Comment 7

> Username: Flast  
> Created_at: 2014-08-19 13:47:50 UTC  
> Url: https://github.com/boostorg/math/pull/5#issuecomment-52635746  

I also vote 1), due to 2) will lead precition issues which hard to understand.  
  
Tentatively, I cherry-pick last commit to clear for merging.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2014-08-19 16:51:26 UTC  
> Url: https://github.com/boostorg/math/pull/5#issuecomment-52663607  

OK, lets make a decision and see who complains ;-)  
  
So lets try (1) as the easiest option, merging...

---

## Comment 9

> Username: Flast  
> Created_at: 2014-08-20 01:29:52 UTC  
> Url: https://github.com/boostorg/math/pull/5#issuecomment-52723739  

Thanks!

---
