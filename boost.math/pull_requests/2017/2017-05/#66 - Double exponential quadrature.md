# #66 Double exponential quadrature. [Closed]

> Username: NAThompson  
> Created at: 2017-05-11 04:04:06 UTC  
> Updated at: 2018-02-10 01:04:01 UTC  
> Closed at: 2017-08-23 18:01:43 UTC  
> Url: https://github.com/boostorg/math/pull/66  

This is ready for review.

---

## Comment 1

> Username: pabristow  
> Created_at: 2017-05-11 07:43:50 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-300709431  

Looks good stuff to me.  An example might be nice, or perhaps just refer (a link say) to the tests that have several working examples (though less clear through the clutter of the Boost.Test infrastructure)?  
  
(Note to self: need to ensure that people have a way of finding new stuff via index entries, better TOC lines, entries in how-did-what, and version specific notes).

---

## Comment 2

> Username: NAThompson  
> Created_at: 2017-05-19 01:03:46 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-302581075  

I'm a bit baffled here; the compile test fails with a linker error. In any case, it caught 3 or 4 bugs.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-06-25 18:06:42 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-310918170  

Moved here for integration testing: https://github.com/boostorg/math/pull/72  
  
I've made changes as follows:  
  
* Some mostly cosmetic stuff to make it Policy-enabled when handling errors to match the rest of Boost.Math.  
* Moved the pre-computed constants in tanh_sinh.hpp to a base class and made them static members since they're constant and we don't need per-instance versions.  Also added float/double/quad precision specializations which use floating point literals rather than lexical_cast's - as well as being more efficient this fixes a Visual Studio issue where conversion of string to float would fail due to too many digits being in the string (!).  Also added true arbitrary precision version with constants computed-on-the-fly.  
* Added remaining tests to Jamfile.v2 and got everything passing locally.  
  
Some general comments/questions:  
  
* I've added some more test cases based on the integral forms of known special functions - one of these - Carlson's elliptic integral RC (see http://www.boost.org/doc/libs/1_64_0/libs/math/doc/html/math_toolkit/ellint/ellint_carlson.html)- integrates highly accurately with exp_sinh integration, but much less so with tanh_sinh - I assume this is to be expected for any given integral some methods behave better than others?  
* There are still issues with multiprecision types which have expression template support enabled (cpp_dec_float for example).  The problem is that the auto keyword and expression templates do not go well together - it's just too easy to end up with dangling references to variables that have gone out of scope: https://github.com/boostorg/multiprecision/issues/19.   I'll deal with this, but it might take a bit of head scratching :(  I _really_ need to device some sort of debugging support in Boost.Multiprecision that will trap this error.  
* I'm wondering about the interfaces to these routines - particularly the tanh_sinh - now that the abscissa and weights are static members there's next to no per-instance data, so we could make this a free function..... but, once the pre-computed coefficients are exhausted, the compute-on-fly scheme seems to be glacially slow?  So... I'm wondering if the constructor should take a max_depth parameter as per the other schemes?  The first few levels can be copied from the pre-computed values and then just the extra levels computed on a per-instance basis.  I'm also wondering if there should be a way (for all of these) to extend the number of levels?  At present, if the starting max_levels is too low, there is no way to step up to more levels except by throwing the integrator away and recalculating all the levels again.  This would also make the 3 methods more consistent with each other - what do you think?  
* There are some "torture test" integrals in this interesting paper (http://crd-legacy.lbl.gov/~dhbailey/dhbpapers/quadrature.pdf) which I'll add as test cases - got to love these "analytically unfathomable" integrals which turn out to have simple closed forms which can be found only by experiment.  
  
Other than that, all looks very good and useful!  Thanks for this, John.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2017-06-25 22:36:59 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-310933220  

> I'm wondering about the interfaces to these routines -  particularly the tanh_sinh - now that the abscissa and weights are static members there's next to no per-instance data, so we could make this a free function..... but, once the pre-computed coefficients are exhausted, the compute-on-fly scheme seems to be glacially slow? So... I'm wondering if the constructor should take a max_depth parameter as per the other schemes?   
  
The reason I calculated the constants at runtime for `exp_sinh` and `sinh_sinh` was that the precomputed constants would overflow the `float` precision, whereas for `tanh_sinh` the precomputed constants would harmlessly underflow to zero. This I still don't know how to solve; what is needed is a compile-time array that shinks if one of the constants overflows, and grows until just before overflow. That's pretty easy at runtime but it's beyond my powers to do this at compile-time . . .  
  
However, since you were able to get the `tanh_sinh` abscissas and weights to arbitrary precision at compile time, I think there's a chance we can get the API uniform between all three. I think the best interface would be a precomputed set of abscissas and weights with a `max_levels` variable which allows more constants to be added during a constructor phase. The runtime calculation of abscissas and weights is indeed glacially slow, but if it's done by the constructor then that cost will only be paid once, and only if the user needs additional precision. This would preclude getting these routines hammered into a free function, but presumably it would be worth the cost? (If we can't get the `exp_sinh` and `sinh_sinh` constants at compile-time, maybe let's just do all three sets at construction?)  
  
Even though throwing away the object if `max_levels` isn't sufficient to obtain the desired accuracy is wasteful, I think that this situation is sufficiently exceptional that it's ok for it to exist. The reason I put it in was because I hit an infinite loop trying to integrate `sin(1/x)` on `(0, 1]`.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2017-06-26 11:50:42 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-311037396  

> The reason I calculated the constants at runtime for |exp_sinh| and   
> |sinh_sinh| was that the precomputed constants would overflow the   
> |float| precision, whereas for |tanh_sinh| the precomputed constants   
> would harmlessly underflow to zero. This I still don't know how to   
> solve; what is needed is a compile-time array that shinks if one of   
> the constants overflows, and grows until just before overflow. That's   
> pretty easy at runtime but it's beyond my powers to do this at   
> compile-time . . .  
>  
> However, since you were able to get the |tanh_sinh| abscissas and   
> weights to arbitrary precision at compile time, I think there's a   
> chance we can get the API uniform between all three. I think the best   
> interface would be a precomputed set of abscissas and weights with a   
> |max_levels| variable which allows more constants to be added during a   
> constructor phase. The runtime calculation of abscissas and weights is   
> indeed glacially slow, but if it's done by the constructor then that   
> cost will only be paid once, and only if the user needs additional   
> precision. This would preclude getting these routines hammered into a   
> free function, but presumably it would be worth the cost? (If we can't   
> get the |exp_sinh| and |sinh_sinh| constants at compile-time, maybe   
> let's just do all three sets at construction?)  
>  
  
OK that sounds like a plan, I'll look at this shortly, but it might be   
after the next release now :(  
  
John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2017-06-29 19:07:31 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-312069908  

So.... I thought I would see how easily I could break tanh-sinh integration, and whether there was anything that could be done about it: not sure about the latter, but it turns out it can be broken rather easily and much sooner than I expected.  
  
Consider integrating (with respect to x):  
  
f(x; a, b) = x^(a - 1) (1-x)^(b-1)  
  
with a = 0.05, b = 3, from 0 to 0.0005.  
  
This is really just an incomplete beta, so the result is known and easily calculated by other means.  The integral has a singularity at x = 0, but more than that, all the really interesting behaviour occurs for x _very_ close to 0.  
  
If we normalise the result (by dividing by beta(a, b)), then we get after a crazy number of levels (about 25 I think it was!):  
  
tanh_sinh: 0.6209467214  
true value: 0.7359375898  
  
Worse, the error estimate (normalised again) was only 0.0000149276 which would suggest that some digits were correct even though none actually were :(  
  
I suspect - but haven't investigated yet - 3 main issues:  
  
* Since we rescale over [-1, 1], there is inherently cancellation error when we calculate abscissa values very close to zero (from values close to -1).  The result is certainly a case of "garbage in, garbage out", with the function being integrated returning largely garbage values just in the area where it's at it's maximum.  This leads me to wonder whether it's worth while modifying sinh-sinh integration to handle the [0, N] case: ie to only require that one limit is zero, the other being either finite or infinite, and in the former case f(x) != 0 at the limit.  Is this likely to help?  
* When we have an integral with all the interesting behaviour crammed into one small section are we wasting time going to deeper levels over most of the range?  Has there been any work on evaluating these depth-first rather than breadth-first?  ie going just deep enough in each sub-interval?    
* When all the interesting behaviour is at one end of the range, we only get right out near the endpoint of the range as the depth increases:  If I have this right, in exp-sinh and sinh-sinh you set the range of the abscissa at runtime, so that right from level 0 we're right at the ends of the range.  For tanh-sinh the abscissa range is fixed, and you just ignore values that would be out of range at our target precision.  That's OK I guess, but we only get right out into the ends of the range as we start subdividing the intervals.  
  
Still thinking yours, John.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2017-06-30 07:32:15 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-312196938  

Update: this particular issue is of course solved rather nicely by a change of variable x -> exp(x).  I realised that while lying in bed last night ;)  
  
I guess the question is how far we want to go to support functions which have many parameters and therefore many different domains each with a different shape?

---

## Comment 8

> Username: pabristow  
> Created_at: 2017-06-30 14:09:38 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-312277227  

From: jzmaddock [mailto:notifications@github.com]  
Sent: 29 June 2017 20:08  
To: boostorg/math  
Cc: Paul A. Bristow; Comment  
Subject: Re: [boostorg/math] Double exponential quadrature. (#66)  
  
So.... I thought I would see how easily I could break tanh-sinh integration, and whether there was anything that could be done about it: not sure about the latter, but it turns out it can be broken rather easily and much sooner than I expected.  
  
<snip>  
  
Worse, the error estimate (normalised again) was only 0.0000149276 which would suggest that some digits were correct even though none actually were :(  
  
I am largely ignorant about this, but IMO if there is anything seriously wrong here,  
  
it’s ‘fibbing’ about how good the estimate is ☹  
  
Paul

---

## Comment 9

> Username: NAThompson  
> Created_at: 2017-06-30 18:21:20 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-312339214  

As John has stated, this has all the hallmarks of rounding error from the domain transformation to (-1, 1). First, as the precision increases (from `float` to `double` to `long double`), the error decreases, but the ULP distance does not, and in fact is increasing. I had suspected this could happen, but in the 500 lines of unit tests, I couldn't find one which caused a problem. What John has done is found an integrand where essentially all the area under the curve is concentrated near the singularity. Most of these integrals are divergent, so it's a great catch.  
  
Another way of thinking about why this broke so badly is that `nextafter(-1)` is `epsilon` away from `-1`, but `nextafter(0)` is a distance `min`  from zero. So we lose a lot of structure we could have queried by transforming the domain.  
  
> This leads me to wonder whether it's worth while modifying sinh-sinh integration to handle the [0, N] case: ie to only require that one limit is zero, the other being either finite or infinite, and in the former case f(x) != 0 at the limit. Is this likely to help?  
  
My current thinking, though I haven't tried it, is to simply not do a domain transformation and simply compute the weights and abscissas on the fly for the range `[a,b]`. This would completely change the API, but I think without it, the rounding error at the singularity cannot be expected to go away.  
  
> When we have an integral with all the interesting behaviour crammed into one small section are we wasting time going to deeper levels over most of the range? Has there been any work on evaluating these depth-first rather than breadth-first? ie going just deep enough in each sub-interval?  
  
This is done in the `exp_sinh` and `sinh_sinh` quadrature, where we know that the integrand must go to zero at infinity to be convergent. However, we don't have any such guarantees over the bounded interval. It sounds like what you want is a form of importance sampling which is nonetheless exponentially convergent by exploiting the analyticity of the integrand. This would be an awesome algorithm, but I  don't know how to do it . . .

---

## Comment 10

> Username: NAThompson  
> Created_at: 2017-06-30 18:53:04 UTC  
> Updated_at: 2017-06-30 18:53:32 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-312346723  

> I am largely ignorant about this, but IMO if there is anything seriously wrong here, it’s ‘fibbing’ about how good the estimate is ☹  
  
Actually, now that I think about it, the error estimate is not for the integral over the open interval (-1,1), the error estimate is for closed interval `[-1+eps, 1-eps]`. But in fact this gives a path forward: Perhaps Richardson extrapolation at the endpoints to get the integral over `[-1, 1]`?

---

## Comment 11

> Username: NAThompson  
> Created_at: 2017-06-30 19:39:53 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-312356298  

I've just confirmed that there is a ton of area under this curve between `[-1, -1 + eps]` (after transforming the domain). So if we didn't do the transformation, we could evaluate very close to zero, but what if the singularity was on both ends? Then one of the singularities would be sampled more coursely, leading to large error again. I think some sort of extrapolation procedure is the only possible solution.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2017-06-30 20:04:33 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-312361152  

On 30/06/2017 20:39, Nick wrote:  
>  
> I've just confirmed that there is a ton of area under this curve   
> between |[-1, -1 + eps]| (after transforming the domain). So if we   
> didn't do the transformation, we could evaluate very close to zero,   
> but what if the singularity was on both ends? Then one of the   
> singularities would be sampled more coursely, leading to large error   
> again. I think some sort of extrapolation procedure is the only   
> possible solution.  
>  
  
On reflection, I'm not sure how easy it is to handle this   
automatically:  there's an awful lot of orders of magnitude between   
epsilon and the smallest representable value - I've seen this in the   
past with the root-finding algorithms - if you're subdividing that   
interval by dividing by 2, you need an awful large number of   
sub-divisions to get all the way down!  If I remember correctly, the   
heuristic I used for the root finding code was to double the (negative)   
exponent at each subdivision when in that area.  But how applicable that   
is to this problem, or how easy it would be to generate abscissa/weight   
pairs is another matter....  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 13

> Username: NAThompson  
> Created_at: 2017-06-30 20:16:01 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-312363324  

> On reflection, I'm not sure how easy it is to handle this  
automatically:  there's an awful lot of orders of magnitude between  
epsilon and the smallest representable value . . .  
  
I think it can be done. We know that the last possible place to evaluate the function is `f(1-eps)`, otherwise we hit the singularity at `f(1)`. I just wrote a little linear extrapolator, using `f(1-eps)` and `f(1-2*eps)`, extrapolated to the "best non-singular estimate" of `f(1)`, and then did a trapezoidal integration over `[1-eps, 1]`. This gave me an order of magnitude improvement (which is, admittedly, not great). But if I do a quadratic extrapolation, I think the improvement can be dramatic.  Of course, then it's not purely a tanh-sinh quadrature, but rather a hybrid of tanh-sinh and newton-cotes on a best quadratic approximation to a singular function, but I think that's an ok compromise . . .

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2017-07-07 19:09:31 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-313768145  

I have a fix for the cancellation issue - it's mentioned briefly in   
Mori's paper, but in more detail Bailey's papers:  basically it's as   
easy to compute 1-x_i as it is to compute x_i, so what I'm experimenting   
with at present is storing x_i for x_i < 0.5 and otherwise x_i-1 (a   
negative value so we can tell which we've stored).  The functor then   
becomes a binary functor that accepts both x_i and 1-x_i as arguments -   
and that allows us to do rescaling without loss of precision when x_i is   
very close to an endpoint.  It also lets us evaluate the function   
arbitrarily close to an endpoint which should improve accuracy and   
convergence. In this test case of the incomplete beta, it gets the right   
answer after just 4 levels with this change (at double precision).  
  
So this looks to be the way to go.... but:  
  
* There are a lot of details in the interface to be worked out, and I've   
only got left-end-is-zero working so far....  
* For some reason the code only works when the initial step size is 1, I   
just can't make it work for any other size... it's a limitation I can   
live with, but annoying that I can't see the bug :(  
* I believe integrals involving (1-x)^alpha are not uncommon, and in   
this case the 1-x_i needs to be passed all the way to the end functor,   
and not just the range adaptors.  
  
Hopefully more soon, John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2017-07-16 18:39:50 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-315628251  

OK, time for an update.  I think I'm mostly done with messing with your tanh_sinh code now, the results are in the pr66 branch here: https://github.com/boostorg/math/pull/72.  
  
The key change is that abscissa values are store as "x" when x is small, and as "x-1" when x is near 1, and this information is available to the function being integrated (if it wants it).  In particular this extra information is used when transforming a range endpoint onto either 0 or infinity, with the result that we now get further out into the corners of these integrals, and pass more accurate abscissa values to them as well.  That means that with the integration limit set to sqrt(epsilon), all the tests bar two pass at 10 epsilon tolerance for double precision.  There are a few that need a higher tolerance when dealing with multiprecision types, but that's mostly down to less accurate std lib function evaluation in those cases, not something intrinsic to the integration routines.    
  
The 2 that don't pass at that tolerance have all the area of the integral close the (non-zero) right limit:  
  
sqrt(log(t)) and log(tan(t))  
  
Even these can be fixed, by passing a 2 argument functor: the first arg is t as before, the second is either `left_limit-t` or `right_limit-t` depending which limit t is closest to (the first is negative the second positive so you can always tell which case you have).  Re-writing the function being integrated to make use of this extra information allows these 2 problem cases to achieve full machine precision and to do it in fewer refinements since they're not "thrashing" around trying to converge based on inaccurate abscissa values any more.  Of these 2 examples could be fixed by variable substitution x -> PI/2 - x as well, but there are other examples that have U shaped curves with all the area at either end - for example integrating ((x+1)(1-x))^-N over (-1,1) with N < 1.  
  
I need to update the docs, and fix a couple of niggles, then see if sinh_sinh and exp_sinh can have pre-computed abscissa/weight pairs as well, but this is pretty close now I think.  If you have any internal real-world test cases and/or any performance tests can you give the new code a try and report back?  
  
Best, John.

---

## Comment 16

> Username: NAThompson  
> Created_at: 2017-08-04 22:19:53 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-320365633  

Hi John,  
  
Just took a look at your code. Amazing improvement to my unsophisticated initial commits. I ran your diffs through `cppcheck` and only found one minor problem (the conditional that always evaluates to false).  
  
I also ran it through AddressSanitizer and UndefinedBehaviorSanitizer; all unit test come back clean. The only problem is the following warning is printed many times:  
  
    detail/tanh_sinh_detail.hpp:584:24707: warning: magnitude of floating-point constant too small for type 'long double'; minimum  
      is 3.64519953188247460253E-4951 [-Wliteral-range]  
  
This warning is harmless as it underflows to zero at runtime, but nonetheless is is annoying. It is fixed by adding the following three lines to `tanh_sinh_detail.hpp`:  
  
    #ifdef __GNUC__  
    #pragma GCC diagnostic ignored "-Wliteral-range"  
    #endif  
  
This feature of `g++` has been supported since 2010, so presumably we're safe adding it?  
  
I think moving the tolerance from the constructor to the evaluation is a good choice; I had thought it simplied the API, but after using the new one I feel like yours is a better choice.  
  
The only problem I see is a loss of some speed. This might just be a single level difference, in which case it can be ignored. I used perf to examine the assembly, and there were *more* hotspots in my code, so that seems to be the most parsimonious hypothesis.

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2017-08-06 12:19:43 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-320503507  

> The only problem I see is a loss of some speed. This might just be a   
> single level difference, in which case it can be ignored. I used perf   
> to examine the assembly, and there were /more/ hotspots in my code, so   
> that seems to be the most parsimonious hypothesis.  
>  
  
I'm not completely surprised if there's a *small* speed loss - as I did   
rather complexify the range adaptors :(  
  
I have some ideas for improving performance, but I think I need to get   
systematic about testing that before trying them out, so more later...  
  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2017-08-06 16:04:23 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-320515832  

> The only problem I see is a loss of some speed. This might just be a   
> single level difference, in which case it can be ignored. I used perf   
> to examine the assembly, and there were /more/ hotspots in my code, so   
> that seems to be the most parsimonious hypothesis.  
>  
  
Ah... there's one change I meant to ask you about and forgot:  there was   
a comment in the tanh_sinh code about always evaluating 4 levels to   
avoid missing any features, but it wasn't being enforced in the code as   
I recall.  So I added that enforcement in.  But.... it's not present in   
the sinh_sinh or exp_sinh code both of which seem fine? For smooth   
functions that change enforces 1 or maybe 2 more levels than before at   
double precision, so you would certainly see it in the tests.  Anyway,   
I'm not sure what the right thing to do is here, we probably need a few   
more pathological test cases to be sure either which way...  
  
Best, John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 19

> Username: NAThompson  
> Created_at: 2017-08-06 20:15:41 UTC  
> Updated_at: 2017-08-06 20:16:12 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-320529778  

> Ah... there's one change I meant to ask you about and forgot:  there was  
a comment in the tanh_sinh code about always evaluating 4 levels to  
avoid missing any features, but it wasn't being enforced in the code as  
I recall.  So I added that enforcement in.  But.... it's not present in  
the sinh_sinh or exp_sinh code both of which seem fine? For smooth  
functions that change enforces 1 or maybe 2 more levels than before at  
double precision, so you would certainly see it in the tests.  Anyway,  
I'm not sure what the right thing to do is here, we probably need a few  
more pathological test cases to be sure either which way...  
  
Unfortunately, the failure of the beta function to be integrated properly has obliterated my confidence in my deductive reasoning. At this point, I think the only way to move forward it to add a massive suite of unit tests, so I'm not convinced that we need enforcement of 4 levels, or not. My pull request into the pr66 branch adds a unit test whereby the strength of the endpoint singularity can be adjusted. It's easy to generate a failure with it; could you take a look and see if it's obvious to you what is happening?

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2017-08-07 19:08:22 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-320753084  

On 06/08/2017 21:15, Nick wrote:  
>  
>     Ah... there's one change I meant to ask you about and forgot:  
>     there was  
>     a comment in the tanh_sinh code about always evaluating 4 levels to  
>     avoid missing any features, but it wasn't being enforced in the  
>     code as  
>     I recall. So I added that enforcement in. But.... it's not present in  
>     the sinh_sinh or exp_sinh code both of which seem fine? For smooth  
>     functions that change enforces 1 or maybe 2 more levels than before at  
>     double precision, so you would certainly see it in the tests. Anyway,  
>     I'm not sure what the right thing to do is here, we probably need  
>     a few  
>     more pathological test cases to be sure either which way...  
>  
> Unfortunately, the failure of the beta function to be integrated   
> properly has obliterated my confidence in my deductive reasoning. At   
> this point, I think the only way to move forward it to add a massive   
> suite of unit tests, so I'm not convinced that we need enforcement of   
> 4 levels, or not. My pull request into this branch adds a unit test   
> whereby the strength of the endpoint singularity can be adjusted. It's   
> easy to generate a failure with it; could you take a look and see if   
> it's obvious to you what is happening?  
>  
  
I think it's going to be easy to generate things which fail: I've only   
looked at the first example you added:  
  
pow(x, -p) / pow(1-x, -p)  
  
and at p = 98/100 the routine takes 15 levels to get to 10^-3 precision :(  
  
However, if we simply change the type to cpp_bin_float_50, then just 5   
levels achieve 3e-50 precision!  
  
In other words it's a numeric stability issue, and the fewer digits our   
type has, the sooner we hit it - I'm assuming that since p is   
necessarily inexact, the pow functions are returning garbage? Update:   
upping the precision just within the f() doesn't help, I think it's that   
f(x) is so sensitive to changes in x, that the .5ulp error in the   
abscissa values is enough to throw off the calculations.  
  
BTW in the case of the incomplete beta integral, although the new code   
handles the last test case I gave you fine, it's still easy to produce   
integrals that have all their area so close to zero that the peak of   
f(x) is smaller than the smallest representable value!  So you end up   
with infinitely small wedges each with infinitely large y values....  
  
John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 21

> Username: NAThompson  
> Created_at: 2017-08-07 21:24:52 UTC  
> Updated_at: 2017-08-07 21:25:20 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-320785018  

> and at p = 98/100 the routine takes 15 levels to get to 10^-3 precision :( However, if we simply change the type to cpp_bin_float_50, then just 5 levels achieve 3e-50 precision!   
  
I guess the question is how good of a numerical integrator can we be expected to produce? Mathematica's numerical integrator also produces garbage periodically, though it spits out a warning to the console (which is not acceptable in boost).

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2017-08-08 17:52:23 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-321032170  

On 07/08/2017 22:24, Nick wrote:  
>  
>     and at p = 98/100 the routine takes 15 levels to get to 10^-3  
>     precision :( However, if we simply change the type to  
>     cpp_bin_float_50, then just 5 levels achieve 3e-50 precision!  
>  
> I guess the question is how good of a numerical integrator can we be   
> expected to produce? Mathematica's numerical integrator also produces   
> garbage periodically, though it spits out a warning to the console,   
> which is not acceptable in boost.  
>  
  
I have a partial fix: if the estimated error starts increasing, then   
we're thrashing around and just abort.  The open question is what abort   
means, we could either  
  
* Stop and return the previous estimate.  Or  
* Raise an evaluation error.  
  
In this particular case, option 1 gives usable (10^-7) precision,   
however I wonder if that is always the case?  We should probably be   
consistent with whatever we do here, as well as what we do when we hit   
an infinity or NaN in I1 (when I0 is usable), or when we run out of levels.  
  
With regard to this particular case, more investigation shows it's not a   
precision issue, but a lack of exponent range: if you create a 53-bit   
multiprecision type with an extended exponent range then it's handled   
just fine, I had a bit of fooling around typing to find an alternative   
transformation that would work, but it seems the exponent range gets you   
very time.  The only option I could find that didn't require   
multiprecision support was a change of variable from x to exp(x), you   
then need to make that substitution algebraically and evaluate by logs,   
so instead of:  
  
    auto f = [&](test_type x)  
    {  
       return pow(x, -p) / pow(1 - x, -p);  
    };  
  
  
Over (0,1) we have:  
  
    auto adapted = [&](test_type x)  
    {  
       test_type l = x * (1 - p) + p * log(1 - exp(x));  
       return exp(l);  
    };  
  
Over (-INF, 0), using 2 * log(numeric_limits<>::min()) as the actual   
lower bound and tanh_sinh integration gave a 9e-16 precision with type   
double.  I'm sure exp_sinh over (-INF, 0) would work just as well in   
this case too.  
  
Best, John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 23

> Username: jzmaddock  
> Created_at: 2017-08-23 18:01:43 UTC  
> Url: https://github.com/boostorg/math/pull/66#issuecomment-324415139  

Merged manually, closing down....

---
