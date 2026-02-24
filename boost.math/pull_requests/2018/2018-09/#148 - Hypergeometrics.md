# #148 Hypergeometrics [Merged]

> Username: NAThompson  
> Created at: 2018-09-03 23:39:49 UTC  
> Updated at: 2019-08-11 14:12:25 UTC  
> Merged at: 2019-08-03 20:54:34 UTC  
> Closed at: 2019-08-03 20:54:34 UTC  
> Url: https://github.com/boostorg/math/pull/148  



---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2018-09-04 00:35:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/148#pullrequestreview-151885021  

📁 include/boost/math/special_functions/hypergeometric_1F1.hpp

```diff
  46 |+          static const char* const function = "boost::math::hypergeometric_1f1<%1%,%1%,%1%>(%1%,%1%,%1%)";
  47 |+ 
  48 |+       if ((z == 0) || (a == 0))
```

> Username: NAThompson  
> Created_at: 2018-09-04 00:35:16 UTC  
> Updated_at: 2019-08-03 09:11:48 UTC  
> Url: https://github.com/boostorg/math/pull/148#discussion_r214765240  

This branch is a set of measure zero. Does it actually improve performance?

> Username: jzmaddock  
> Created_at: 2018-09-04 07:15:35 UTC  
> Updated_at: 2019-08-03 09:11:48 UTC  
> Url: https://github.com/boostorg/math/pull/148#discussion_r214807967  

Probably force of habit to pick off trivial special cases first - if you think it's better without, try removing it and seeing what happens ;)

> Username: NAThompson  
> Created_at: 2018-09-04 10:02:04 UTC  
> Updated_at: 2019-08-03 09:11:48 UTC  
> Url: https://github.com/boostorg/math/pull/148#discussion_r214857038  

I think we need a separable PDE which has 1F1 as a component. Then there will be an associated series of the form 1F1(a_n,b_n,x)g_n(y) (something like that) and it will give us some idea of whether practical use generates parameter values with sufficient frequency to make this worthwhile.  
  
I'll go ahead and write a benchmark to find out what the performance impact is; you're probably right that it's negligible.

> Username: jzmaddock  
> Created_at: 2018-09-04 11:13:21 UTC  
> Updated_at: 2019-08-03 09:11:48 UTC  
> Url: https://github.com/boostorg/math/pull/148#discussion_r214875542  

It's not only performance - the result should be exactly 1 in that situation, regardless of the other arguments.


---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2018-09-04 00:36:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/148#pullrequestreview-151885109  

📁 include/boost/math/special_functions/hypergeometric_1F1.hpp

```diff
  58 |+ 
  59 |+       // other checks:
  60 |+       if (a == -1)
```

> Username: NAThompson  
> Created_at: 2018-09-04 00:36:08 UTC  
> Updated_at: 2019-08-03 09:11:48 UTC  
> Url: https://github.com/boostorg/math/pull/148#discussion_r214765318  

Another branch off a set of measure zero.

> Username: jzmaddock  
> Created_at: 2018-09-04 07:15:57 UTC  
> Updated_at: 2019-08-03 09:11:48 UTC  
> Url: https://github.com/boostorg/math/pull/148#discussion_r214808073  

As above, force of habit.


---

## Review 3 [Commented]

> Username: NAThompson  
> Created_at: 2018-09-04 00:36:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/148#pullrequestreview-151885154  

📁 include/boost/math/special_functions/hypergeometric_1F1.hpp

```diff
  63 |+       const T b_minus_a = b - a;
  64 |+ 
  65 |+       // 0f0 (exp) case;
```

> Username: NAThompson  
> Created_at: 2018-09-04 00:36:59 UTC  
> Updated_at: 2019-08-03 09:11:48 UTC  
> Url: https://github.com/boostorg/math/pull/148#discussion_r214765348  

These next three branches are for where people don't need to be using 1F1.

> Username: jzmaddock  
> Created_at: 2018-09-04 07:17:32 UTC  
> Updated_at: 2019-08-03 09:11:48 UTC  
> Url: https://github.com/boostorg/math/pull/148#discussion_r214808428  

For sure, but I don't see why the user should be forced to test for degenerate cases before calling 1F1 - you won't necessarily know  in advance whether some of the values you're passing in are degenerate or not.


---

## Review 4 [Commented]

> Username: NAThompson  
> Created_at: 2018-09-04 00:40:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/148#pullrequestreview-151885334  

📁 include/boost/math/special_functions/hypergeometric_1F1.hpp

```diff
 104 |+             return detail::hypergeometric_1f1_pade(b, z, pol);
 105 |+ 
 106 |+          // Let's otherwise make z positive (almost always)
```

> Username: NAThompson  
> Created_at: 2018-09-04 00:40:18 UTC  
> Updated_at: 2019-08-03 09:11:48 UTC  
> Url: https://github.com/boostorg/math/pull/148#discussion_r214765519  

I don't understand why you'd want to make z > 0. If z < 0, the series alternates. But of course there are details that may make this naive understanding incorrect.

> Username: jzmaddock  
> Created_at: 2018-09-04 07:22:35 UTC  
> Updated_at: 2019-08-03 09:11:48 UTC  
> Url: https://github.com/boostorg/math/pull/148#discussion_r214809650  

I believe the following logic is only good for z > 0, certainly this simplifies the logic a good deal.  It was inherited from the original SOC implementation though.


---

## Comment 5

> Username: ckormanyos  
> Created_at: 2018-09-04 18:23:28 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-418469554  

Wow! I had almost forgotten about this work. That is really cool stuff. If there is any interest, there is also a Chebyshev coefficient expansion for hypergeometric_1f1 in the original work, but only on my local data base. This expansion is designed for real argument less than zero. Chris.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2018-10-20 12:30:45 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-431576046  

Update: I've made some good progress nailing down the issues but hit an impasse with the way things are currently architected.  Basically 2 things that I wondered if they were a problem have jumped up and stomped up and down on things:  
  
1) The individual terms in the expansions (and also in the indirect transformations like Kummers relation and/or the recurrences) can grow arbitrarily large or small.... but then mostly cancel out at the end.  One solution I have is to maintain a scaling factor e^N for some integer N.  Each time we would over/underflow we rescale by e^N_i and keep a running total of all the N_i's and then rescale back at the end.  This works because all the scaling factors are integers and can be totalled up exactly without cancellation error.  Unfortunately this requires a bit of a rewrite.  
2) There is an issue with the series definition when *b* is negative as the series can suddenly jump back to life as *b+k* passes the origin.  I have an example with really rather ordinary looking arguments where this causes the sum to converge prematurely and be many orders of magnitude out.  This is also rather prone to over/underflow so requires (1).  Jumping direct to the floor(b)'th term in order to check for this issue is also somewhat problematic as the terms grow truly huge.  
  
Hopefully more soon....

---

## Comment 7

> Username: NAThompson  
> Created_at: 2018-10-21 02:09:32 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-431632763  

> One solution I have is to maintain a scaling factor e^N for some integer N.  
  
Wouldn't (say) 2^N be more precise and faster?  
  
> There is an issue with the series definition when b is negative as the series can suddenly jump back to life as b+k passes the origin.   
  
Is there a failing unit test? I ran `test_hypergeometric_distN`, where `N=0,1,2,3,4,5`, but didn't find a failure.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2018-10-21 09:32:32 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-431653138  

> Wouldn't (say) 2^N be more precise and faster?  
  
Good point - this began with Kummers relation where you're multiplying by e^z, so it was natural to start with e^floor(z) as the scaling factor.  There are other places where 2^N would be more natural (in the series for example).  
  
At the moment I'm struggling to find a good way of converting  e^z to 2^N . e^y, ideas?  
  
>Is there a failing unit test? I ran test_hypergeometric_distN, where N=0,1,2,3,4,5, but didn't find a failure.  
  
That's the hypergeometric distribution, not function.  The test case for 1F1 is test_1f1.cpp, but the failing example (one of many) is:  
  
a = -835.009, b = -1252.51, z = -1043.76  
  
Probably if I searched hard enough a b value very close to a negative integer would show the same effect with smaller arguments - that's just the first one that came up with random args.  
  
BTW that case proceeds via Kummers relation, so we're actually evaluating:  
  
a = -417.501, b = -1252.51, z = 1043.76  
  
which has all positive terms and converges reasonably well to 1e117 in ~ 300 terms, but then has another "spasm" of activity as b crosses the origin to give a final result of -1.3e434 !

---

## Comment 9

> Username: NAThompson  
> Created_at: 2018-10-21 18:16:43 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-431691204  

> which has all positive terms and converges reasonably well to 1e117 in ~ 300 terms, but then has another "spasm" of activity as b crosses the origin to give a final result of -1.3e434 !  
  
Yikes. How about [this formula](http://functions.wolfram.com/HypergeometricFunctions/Hypergeometric1F1/06/02/0003/)? It's valid for large |z| and a rising factorial of b does not appear in the denominator.

---

## Comment 10

> Username: NAThompson  
> Created_at: 2018-10-22 15:52:48 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-431876265  

Quick question: I'm trying to run `test_1F1.cpp`, but looks like it hasn't been added to `test/Jamfile.v2`, and I'm getting a compile error from `#include <boost/math/tools/test.hpp>`, which seems to not exist. Is this just some cleanup that needs to be done?

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2018-10-22 17:05:02 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-431899302  

>Quick question: I'm trying to run test_1F1.cpp, but looks like it hasn't been added to test/Jamfile.v2, and I'm getting a compile error from #include <boost/math/tools/test.hpp>, which seems to not exist. Is this just some cleanup that needs to be done?  
  
Ah it's under libs/math/include_private/  
  
We got flack for private/testing headers being under boost-root/boost so they got moved here, I should probably have changed all the include paths to something relative to make it easier to build outside of the Jamfile but life was is too short :(  
  
Thanks for the expm1 fix BTW.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2018-10-22 18:02:07 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-431917235  

Tests should all build from the Jamfile now.  
  
Don't be fazed too much by the number of failures in the 1F1 tests - many of them will be the same issues over and over it's "just" a case of working through them...

---

## Comment 13

> Username: NAThompson  
> Created_at: 2018-10-22 20:14:54 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-431966968  

Fixing:  
  
```  
./test_1f1.hpp:25:10: warning: non-portable path to file '<boost/math/special_functions/hypergeometric_1F1.hpp>'; specified path differs in case from file name on disk [-Wnonportable-include-path]  
```  
  
by using `pFq` casing everywhere. Hopefully, that's the least annoying decision. Also, I get the compile error:  
  
```  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./test_0F1.hpp:185:566: error: non-aggregate type 'boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<40, int, void>, boost::multiprecision::et_on>' cannot be initialized with an initializer list  
      {SC_(-0.25), SC_(-3000.25), SC_(39.685967392358901409909771219162599134220400862017)}, {  
```  
  
Not quite sure how to work through that one.

---

## Comment 14

> Username: NAThompson  
> Created_at: 2018-10-23 16:36:43 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-432323957  

From Fredrik Johansson's paper on [numerical evaluation of hypergeometrics](http://fredrikj.net/math/hypgeom.pdf):  
  
> Unfortunately, Mathematica’s heuristic error tracking is unreliable. For example,  
f[-1000,1,1.0], produces −1.86254761018·109 without any hint that the result is wrong  
  
It looks like spot tests using Mathematica might lead to deep frustration. I've therefore played the same game we played with the Lambert-W function, namely testing that a quadrature of `1F1` produces a known value. For the parameters I tested, I found that whenever it returned a finite value, the value was correct, but it was very easy to get it to blow up and return `nan`. Since I'm not really familiar with the expected behavior of `1F1` for various values of the parameters, I just left the tests in a passing state. But I think these will be useful tests, as they will allow us to make claims of the form 'for a given `(a, b)`, `1F1(a,b,t)` is correct for all t.'

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2018-10-23 17:55:32 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-432352789  

Thanks for the extra tests - they're a good idea - I suspect though that the small values of the parameters tested are very much in the "easy-peasy" end of things ;)  
  
A couple of comments on the large-z asymptotics currently in git - these were inherited from the summer-of-code work and so far as I can tell the selection condition for these is such that they are never called!  The implementation is equivalent to https://dlmf.nist.gov/13.7#E1 I believe, though in the complex case we would need https://dlmf.nist.gov/13.7#E2 as per  Johansson's paper referenced above.  
The other comment is that it's just as well these are never called because I've broken 2F0 - well actually I fixed it but it breaks this use case!  The issue is that 2F0 will *eventually* shoot off to infinity unless one of the *a* parameters is a negative integer (polynomial case).  However in the asymptotic use case, we take the initial convergent part and ignore the infinite tail.  Possibly I just need to provide 2 entry points for 2F0 - with different error handling - one for the regular case and one for the asymptotic case?  Or maybe the asymptotic case is the only one that's of any use?  
  
And finally.... I'm not sure about the change to CamelCase file names: function names yes since a capitol F is well established practice - not sure what difficulty the mixed case filenames might cause though?  We'll leave it where it is for now though, and maybe we should get some more opinions on this later.

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2018-10-23 19:11:39 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-432380847  

>Also, I get the compile error:  
>  
>~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
>./test_0F1.hpp:185:566: error: non-aggregate type >'boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<40, int, void>, >boost::multiprecision::et_on>' cannot be initialized with an initializer list  
>      {SC_(-0.25), SC_(-3000.25), SC_(39.685967392358901409909771219162599134220400862017)},   
  
There were some stray {}'s which had crept in, should be fixed now.

---

## Comment 17

> Username: NAThompson  
> Created_at: 2018-10-23 19:38:25 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-432389729  

> I'm not sure about the change to CamelCase file names: function names yes since a capitol F is well established practice - not sure what difficulty the mixed case filenames might cause though?  
  
Whatever casing convention causes the least pain is fine with me; I figured matching the casing of the calls is reasonable. I was a bit embarrassed by that commit lest I be mistaken for a bike-shed warrior, but I swear it fixed a real problem!  
  
> There were some stray {}'s which had crept in, should be fixed now.  
  
I've validated the fix works on my system.  
  
> A couple of comments on the large-z asymptotics currently in git - these were inherited from the summer-of-code work and so far as I can tell the selection condition for these is such that they are never called!   
  
We're in branch hell with this. Hopefully I can start contributing meaningfully now that I've got my hand dirty in the code.

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2018-10-24 18:46:42 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-432781730  

>I was a bit embarrassed by that commit lest I be mistaken for a bike-shed warrior, but I swear it fixed a real problem!  
  
LOL, paint in green man, paint it green! :)  
  
Meanwhile I have an improved large-z asymptotic almost working... more soon.

---

## Comment 19

> Username: NAThompson  
> Created_at: 2018-12-23 17:35:56 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-449652368  

I count only 92 failures from 1F1 now; there were what, 500 before the last set of commits? Truly an astonishingly complicated function. Will it be the most complicated special function by LOC ever for this repo?

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2018-12-23 18:10:16 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-449654357  

The current failures are all from thrown exceptions saying "Help: I can't cope with this" or words to that effect ;)  They are to some extent liveable-with, but I haven't quite exhausted all the options just yet!  
  
Also, as part of the last commits, added a function which calculates F_n+1/F_n given nothing more than a 3-term recurrence relation.  I admit to being a true nerd for these things but I happen to think it's beautifully simple.  Mathematically it's the same as the well known Miller's method, but by converting the recurrence relation into a continued fraction and feeding into our existing routine that, becomes a one-liner.  And unlike Miller's method, you don't need to know how many terms will be needed up front.  Seems to converge super-fast and accurate as well.  The method goes back to 1965, but seems to have got lost thanks to Numerical Recipes publicising Miller's method.  
  
Unfortunately applications to 1F1 is limited as we can get into domains where either there is no minimal solution, or else the method converges to a different solution to the one we want :(  If I had more theoretical nous, I suspect there's a paper or 3 there investigating that.  
  
As for complexity.... it will end up being not to bad, the problem really is the lack of suitable methods to try in the "difficult" domains.  Plus a lack of decent literature to follow!  
  
More soon... probably.

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2019-01-11 09:07:19 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-453441347  

Nearly there, but somewhat stuck on one domain: https://mathoverflow.net/questions/320588/on-the-convergence-of-millers-algorithm-for-special-function-evaluation-hyperg

---

## Comment 22

> Username: NAThompson  
> Created_at: 2019-01-11 17:36:25 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-453596473  

Not sure if you're quite ready for the fine-tooth comb, but I did take a look:  
  
I couldn't get this equation to actually render as an equation in the docs:  
  
```  
sum[sub n=0]^[super [infin]] a[super (n)]/b[super (n)] x[super n]/n!  
```  
  
I have no clue why. Nor do I know how to escape primes, so `w'` causes docbook to choke.  
  
Ran the unit tests and found:  
  
```bash  
test$ ../../../b2 cxxflags="-Wall --std=gnu++17 -fsanitize=address -fsanitize=undefined" linkflags="-fsanitize=address -fsanitize=undefined" test_1F1  
../../../boost/math/special_functions/detail/hypergeometric_1F1_bessel.hpp:65:17: runtime error: division by zero  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/math/special_functions/detail/hypergeometric_1F1_bessel.hpp:65:17 in  
```  
  
Turns out this is a harmless ub, since in the next line you have:  
  
```  
if ((term == 0) || !(boost::math::isfinite)(term))  
```  
  
but since many people live with UBSan and ASan running, it might be nice just to handle the division by zero explicitly. (I'd just fix it, but I imagine you'd get into an annoying merge conflict if I did. . . )

---

## Comment 23

> Username: NAThompson  
> Created_at: 2019-01-12 15:48:26 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-453757681  

I wonder if boost.odeint has something fast enough that you could just use a stepper through the challenging regions. It looks like they have a Bulirsch-Stoer which is reputed to be very good.

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2019-01-18 19:41:38 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-455664572  

Time for a status update - most things now work and the tests all pass locally except for higher-precision types (see below).  
  
Still Outstanding:  
* 1F1(a, -b, z) is still an issue for large b.  I've disabled the tests for these for now as there appears to be no viable method.  The Tricomi methods will work *if* the type has enough exponent range, or I can modify the Bessel function implementations to rescale the result.  Inefficient though as J_-v(x) is O(v).  
* Many of the implementation methods are brain-dead and inefficient, the fixes are straightforward though.  
* There is an issue for 1F1(tiny, -b, -z) when the precision is higher than double - the approximation currently used becomes divergent before convergence, I need to see if there's another method that will work.  
* It all needs documenting, probably more testing as well!

---

## Comment 25

> Username: NAThompson  
> Created_at: 2019-01-19 04:25:28 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-455748504  

All looks good to me. The only problem I had while running your tests was that the `test_1F1_dec_40` took *forever* to compile. I gave it five minutes on Apple clang before I gave up on it.

---

## Comment 26

> Username: jzmaddock  
> Created_at: 2019-01-19 09:12:30 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-455763039  

>All looks good to me. The only problem I had while running your tests was that the test_1F1_dec_40 took forever to compile. I gave it five minutes on Apple clang before I gave up on it.  
  
Ouch.  I haven't really looked at that one yet as there are still known issues with precision > double.  I suspect it's instantiating nearly all the special functions (at least gamma, bessel J,Y,I,K and zeta) and that's a heck of a lot of expression templates!  Either way 5 minutes is too long....

---

## Comment 27

> Username: NAThompson  
> Created_at: 2019-01-31 20:52:45 UTC  
> Updated_at: 2019-01-31 20:53:09 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-459502137  

It looks like `expm1` is not part of the real concepts?  
  
```bash  
$../../../b2 cxxflags="-Wfatal-errors -Wall --std=gnu++17 -fsanitize=address -fsanitize=undefined" linkflags="-fsanitize=address -fsanitize=undefined" test_1F1_real_concept  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes (cached)  
    - Boost.Config Feature Check: cxx11_lambdas : yes (cached)  
    - Boost.Config Feature Check: cxx11_unified_initialization_syntax : yes (cached)  
    - Boost.Config Feature Check: cxx11_smart_ptr : yes (cached)  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes (cached)  
    - Boost.Config Feature Check: cxx11_lambdas : yes (cached)  
    - Boost.Config Feature Check: cxx11_unified_initialization_syntax : yes (cached)  
    - Boost.Config Feature Check: cxx11_smart_ptr : yes (cached)  
    - has_icu builds           : no  (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : no  (cached)  
...patience...  
...patience...  
...found 4966 targets...  
...updating 9 targets...  
darwin.compile.c++ ../../../bin.v2/libs/math/test/test_1F1_real_concept.test/darwin-4.2.1/debug/link-static/visibility-hidden/test_1F1.o  
In file included from test_1F1.cpp:6:  
./test_1F1.hpp:142:15: fatal error: no matching function for call to 'expm1'  
   expected = expm1(T(36.25)) / T(36.25);  
              ^~~~~  
./test_1F1.hpp:165:4: note: in instantiation of function template specialization 'test_spots5<boost::math::concepts::real_concept>' requested here  
   test_spots5(z, type_name);  
   ^  
test_1F1.cpp:161:4: note: in instantiation of function template specialization 'test_spots<boost::math::concepts::real_concept>' requested here  
   test_spots(boost::math::concepts::real_concept(0.1), "real_concept");  
   ^  
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.14.sdk/usr/include/math.h:369:15: note: candidate function not viable: no known conversion from 'boost::math::concepts::real_concept' to 'double' for 1st argument  
extern double expm1(double);  
              ^  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/include/c++/v1/math.h:1135:46: note: candidate function not viable: no known conversion from 'boost::math::concepts::real_concept' to 'float' for 1st argument  
inline _LIBCPP_INLINE_VISIBILITY float       expm1(float __lcpp_x) _NOEXCEPT       {return ::expm1f(__lcpp_x);}  
                                             ^  
```

---

## Comment 28

> Username: jzmaddock  
> Created_at: 2019-02-04 18:27:10 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-460357719  

Status update:  I've kicked off a CI test for this one now as the code is more or less finished off.  
  
* Performance is still on the slow side, but may be as good as it's going to get.  
* Docs still need a lot of work.  
* Some of the side features I implemented as part of this (Bessel iterators and ratios) need rounding off and documenting.  
* I need to write a program to saturation test 1F1 and probe for issues I haven't found yet - this is probably the highest priority as there are surely bugs in there somewhere.  
  
Other than that, feel free to give it a try.

---

## Comment 29

> Username: NAThompson  
> Created_at: 2019-02-05 03:05:59 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-460497098  

Just tested this: `1F1_integrals`, `1F1_float`,  and `1F1_double` work flawlessly.  
  
`1F1_long_double` needs a bit higher error rate (as you'll learn from the build) and has some (probably harmless UB):  
  
```  
Testing Small random values with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
hypergeometric_1F1<long double> Max = 864.8 RMS Mean=45.32  
    worst case at row: 209  
    { 12.588947296142578125, -11.15863800048828125, -14.580921173095703125, 117.9683534004054212 }  
  
  
../../../boost/math/special_functions/detail/hypergeometric_1F1_bessel.hpp:69:17: runtime error: division by zero  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/math/special_functions/detail/hypergeometric_1F1_bessel.hpp:69:17 in  
Testing Large random values with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
hypergeometric_1F1<long double> Max = 8.501e+04 RMS Mean=2652  
    worst case at row: 2358  
    { 814723.5, 13586.87890625, -15.87335205078125, 7.3454325098384925824e-430 }  
  
  
Testing Large random values - double limited precision with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
hypergeometric_1F1<long double> Max = 378 RMS Mean=261.4  
    worst case at row: 0  
    { -9.6886797109618782997e-06, -1252.51318359375, -1043.7607421875, 0.99998253928322852692 }  
  
  
Testing special cases for type long double  
./test_1F1.hpp:148: error: in "test_main": difference{6.99441e-19} between computed{152709817481984.108521} and expected{152709817481984.108627} exceeds 4.33681e-17%  
```  
  
`test_1F1_quad` compiled for 3 minutes, and then I gave up on it. Strangely, it looks like the compile hung?  
  
```  
time ../../../b2 cxxflags="-Wfatal-errors -Wall --std=gnu++17 -fsanitize=address -fsanitize=undefined" linkflags="-fsanitize=address -fsanitize=undefined" test_1F1_quad  
boost-install.jam: No such file or directory  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes (cached)  
    - Boost.Config Feature Check: cxx11_lambdas : yes (cached)  
    - Boost.Config Feature Check: cxx11_unified_initialization_syntax : yes (cached)  
    - Boost.Config Feature Check: cxx11_smart_ptr : yes (cached)  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes (cached)  
    - Boost.Config Feature Check: cxx11_lambdas : yes (cached)  
    - Boost.Config Feature Check: cxx11_unified_initialization_syntax : yes (cached)  
    - Boost.Config Feature Check: cxx11_smart_ptr : yes (cached)  
    - has_icu builds           : no  (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : no  (cached)  
...patience...  
...patience...  
...found 5017 targets...  
...updating 4 targets...  
darwin.compile.c++ ../../../bin.v2/libs/math/test/test_1F1_quad.test/darwin-4.2.1/release/link-static/threading-multi/visibility-hidden/test_1F1.o  
^C  
../../../b2  linkflags="-fsanitize=address -fsanitize=undefined" test_1F1_qua  2.18s user 0.47s system 1% cpu 3:06.63 total  
```   
  
Only 2.18s of user time, 3 minutes total, 1% cpu consumption? In any case, this appears to be a different issue.  
  
```  
$ c++ --version  
Apple LLVM version 10.0.0 (clang-1000.11.45.5)  
```  
  
Obtained 14 errors from `test_2F0`:  
  
```  
Testing Random non-integer a2, |z| > 1 with type cpp_bin_float_quad  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
hypergeometric_0F1<cpp_bin_float_quad> Max = 1502 RMS Mean=42.01  
    worst case at row: 9  
    { -20, -14.580921173095703125, -21.8713836669921875, -4.5823620266905623948427865564377975e+39 }  
Peak error greater than expected value of 1  
./handle_test_result.hpp:172: error: in "test_main": check bounds.first >= max_error_found has failed  
Mean error greater than expected value of 1  
./handle_test_result.hpp:177: error: in "test_main": check bounds.second >= mean_error_found has failed  
  
  
Testing Integer a2, |z| > 1 with type cpp_bin_float_quad  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
hypergeometric_0F1<cpp_bin_float_quad> Max = 279.4 RMS Mean=9.25  
    worst case at row: 450  
    { -13, -15, -2.23172760009765625, -134223055255683.95671418188003358613 }  
Peak error greater than expected value of 1  
./handle_test_result.hpp:172: error: in "test_main": check bounds.first >= max_error_found has failed  
Mean error greater than expected value of 1  
./handle_test_result.hpp:177: error: in "test_main": check bounds.second >= mean_error_found has failed  
  
  
Testing a1 = a2 + 0.5 with type cpp_bin_float_quad  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
hypergeometric_0F1<cpp_bin_float_quad> Max = 286.1 RMS Mean=23.92  
    worst case at row: 1  
    { -20, -19.5, -3.64523029327392578125, -58552438403993540075445057.576807514 }  
Peak error greater than expected value of 1  
./handle_test_result.hpp:172: error: in "test_main": check bounds.first >= max_error_found has failed  
Mean error greater than expected value of 1  
./handle_test_result.hpp:177: error: in "test_main": check bounds.second >= mean_error_found has failed  
```

---

## Comment 30

> Username: NAThompson  
> Created_at: 2019-02-08 02:03:38 UTC  
> Updated_at: 2019-02-08 02:04:03 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-461665172  

In fact there are many domains of 0F1 and 1F1 that are quite fast:  
  
```cpp  
#include <benchmark/benchmark.h>  
#include <array>  
#include <random>  
#include <iostream>  
#include <algorithm>  
#include <boost/math/special_functions/hypergeometric_0F1.hpp>  
#include <boost/math/special_functions/hypergeometric_1F1.hpp>  
  
template<class Real>  
void BM_1F0(benchmark::State& state)  
{  
    using boost::math::hypergeometric_0F1;  
  
    std::default_random_engine gen;  
    std::uniform_real_distribution<Real> x_dis(state.range(0), 2*state.range(0));  
    std::uniform_real_distribution<Real> b_dis(state.range(1), 2*state.range(1));  
  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(hypergeometric_0F1(b_dis(gen), x_dis(gen)));  
    }  
}  
  
BENCHMARK_TEMPLATE(BM_1F0, double)->RangeMultiplier(2)->Ranges({{-64, 64},{-64, 64}});  
  
  
template<class Real>  
void BM_1F1(benchmark::State& state)  
{  
    using boost::math::hypergeometric_1F1;  
  
    std::default_random_engine gen;  
    std::uniform_real_distribution<Real> x_dis(state.range(0), 2*state.range(0));  
    std::uniform_real_distribution<Real> b_dis(state.range(1), 2*state.range(1));  
    std::uniform_real_distribution<Real> c_dis(state.range(2), 2*state.range(2));  
  
    for (auto _ : state)  
    {  
        benchmark::DoNotOptimize(hypergeometric_1F1(b_dis(gen), c_dis(gen), x_dis(gen)));  
    }  
}  
  
BENCHMARK_TEMPLATE(BM_1F1, double)->RangeMultiplier(2)->Ranges({{-64, 64},{-64, 64}, {-64, 64}});  
BENCHMARK_MAIN();  
```  
  
  
```  
------------------------------------------------------------------  
Benchmark                           Time           CPU Iterations  
------------------------------------------------------------------  
BM_1F0<double>/-64/-64            145 ns        145 ns    4808947  
BM_1F0<double>/1/-64               75 ns         75 ns    9123850  
BM_1F0<double>/2/-64               80 ns         80 ns    8749344  
BM_1F0<double>/4/-64               85 ns         85 ns    8200658  
BM_1F0<double>/8/-64               96 ns         96 ns    7314142  
BM_1F0<double>/16/-64             108 ns        108 ns    6468126  
BM_1F0<double>/32/-64             125 ns        125 ns    5625472  
BM_1F0<double>/64/-64             147 ns        147 ns    4694552  
BM_1F0<double>/-64/1             1591 ns       1578 ns     458764  
BM_1F0<double>/1/1                 98 ns         98 ns    7174189  
BM_1F0<double>/2/1                107 ns        107 ns    6448819  
BM_1F0<double>/4/1                122 ns        122 ns    5831341  
BM_1F0<double>/8/1                135 ns        135 ns    5049376  
BM_1F0<double>/16/1               154 ns        154 ns    4594502  
BM_1F0<double>/32/1               181 ns        179 ns    3797022  
BM_1F0<double>/64/1               210 ns        208 ns    3362233  
BM_1F0<double>/-64/2             1648 ns       1628 ns     453821  
BM_1F0<double>/1/2                102 ns        100 ns    7295694  
BM_1F0<double>/2/2                107 ns        106 ns    6605705  
BM_1F0<double>/4/2                121 ns        120 ns    5936782  
BM_1F0<double>/8/2                142 ns        139 ns    5110347  
BM_1F0<double>/16/2               151 ns        151 ns    4511850  
BM_1F0<double>/32/2               171 ns        171 ns    4089645  
BM_1F0<double>/64/2               200 ns        200 ns    3526484  
BM_1F0<double>/-64/4             1501 ns       1498 ns     469241  
BM_1F0<double>/1/4                 92 ns         92 ns    7650106  
BM_1F0<double>/2/4                104 ns        103 ns    6981081  
BM_1F0<double>/4/4                115 ns        115 ns    5847514  
BM_1F0<double>/8/4                128 ns        128 ns    5553264  
BM_1F0<double>/16/4               156 ns        152 ns    4353884  
BM_1F0<double>/32/4               169 ns        169 ns    4219638  
BM_1F0<double>/64/4               226 ns        214 ns    3547933  
BM_1F0<double>/-64/8             1339 ns       1336 ns     524808  
BM_1F0<double>/1/8                 91 ns         90 ns    8104197  
BM_1F0<double>/2/8                111 ns        104 ns    6385580  
BM_1F0<double>/4/8                108 ns        108 ns    6284396  
BM_1F0<double>/8/8                126 ns        124 ns    5687496  
BM_1F0<double>/16/8               138 ns        138 ns    5083921  
BM_1F0<double>/32/8               192 ns        178 ns    4380503  
BM_1F0<double>/64/8               271 ns        228 ns    2790056  
BM_1F0<double>/-64/16            1562 ns       1132 ns     602047  
BM_1F0<double>/1/16               121 ns        101 ns    7119972  
BM_1F0<double>/2/16               138 ns        108 ns    6513385  
BM_1F0<double>/4/16               171 ns        127 ns    6228534  
BM_1F0<double>/8/16               158 ns        133 ns    4354778  
BM_1F0<double>/16/16              134 ns        133 ns    4675738  
BM_1F0<double>/32/16              190 ns        189 ns    3670494  
BM_1F0<double>/64/16              197 ns        197 ns    3350501  
BM_1F0<double>/-64/32             591 ns        547 ns    1187931  
BM_1F0<double>/1/32                81 ns         81 ns    7090474  
BM_1F0<double>/2/32               117 ns        101 ns    6158448  
BM_1F0<double>/4/32               117 ns        111 ns    6260173  
BM_1F0<double>/8/32               119 ns        117 ns    5672517  
BM_1F0<double>/16/32              145 ns        135 ns    5665998  
BM_1F0<double>/32/32              138 ns        135 ns    4831551  
BM_1F0<double>/64/32              173 ns        168 ns    4314144  
BM_1F0<double>/-64/64             323 ns        322 ns    2016402  
BM_1F0<double>/1/64                82 ns         80 ns    9036222  
BM_1F0<double>/2/64               122 ns         95 ns    6399122  
BM_1F0<double>/4/64                88 ns         88 ns    7639836  
BM_1F0<double>/8/64                99 ns         97 ns    6838008  
BM_1F0<double>/16/64              105 ns        105 ns    6775133  
BM_1F0<double>/32/64              121 ns        120 ns    5955470  
BM_1F0<double>/64/64              163 ns        150 ns    5190145  
BM_1F1<double>/-64/-64/-64       4258 ns       4238 ns     157002  
BM_1F1<double>/1/-64/-64        17128 ns      16994 ns      39170  
BM_1F1<double>/2/-64/-64        30687 ns      30209 ns      23847  
BM_1F1<double>/4/-64/-64        53443 ns      49300 ns      12759  
BM_1F1<double>/8/-64/-64        61984 ns      61627 ns      10929  
BM_1F1<double>/16/-64/-64       96883 ns      95601 ns       7379  
BM_1F1<double>/32/-64/-64      135518 ns     132036 ns       4935  
BM_1F1<double>/64/-64/-64      150282 ns     149329 ns       4439  
BM_1F1<double>/-64/1/-64       180073 ns     173284 ns       3778  
BM_1F1<double>/1/1/-64           1715 ns       1689 ns     420880  
BM_1F1<double>/2/1/-64           1668 ns       1387 ns     441273  
BM_1F1<double>/4/1/-64           1319 ns       1314 ns     507507  
BM_1F1<double>/8/1/-64           1425 ns       1420 ns     499426  
BM_1F1<double>/16/1/-64          1808 ns       1801 ns     388986  
BM_1F1<double>/32/1/-64          3768 ns       3701 ns     194430  
BM_1F1<double>/64/1/-64          4229 ns       4185 ns     173600  
BM_1F1<double>/-64/2/-64       351629 ns     350240 ns       1917  
BM_1F1<double>/1/2/-64           1508 ns       1505 ns     467131  
BM_1F1<double>/2/2/-64           1444 ns       1359 ns     573075  
BM_1F1<double>/4/2/-64           2132 ns       1685 ns     460648  
BM_1F1<double>/8/2/-64           3319 ns       2075 ns     343656  
BM_1F1<double>/16/2/-64          4571 ns       3230 ns     198666  
BM_1F1<double>/32/2/-64          6701 ns       5444 ns     100000  
BM_1F1<double>/64/2/-64          9273 ns       6604 ns     108934  
BM_1F1<double>/-64/4/-64       382624 ns     374557 ns       1820  
BM_1F1<double>/1/4/-64           1762 ns       1728 ns     379124  
BM_1F1<double>/2/4/-64           1661 ns       1521 ns     476959  
BM_1F1<double>/4/4/-64           1745 ns       1616 ns     445775  
BM_1F1<double>/8/4/-64           1704 ns       1633 ns     418806  
BM_1F1<double>/16/4/-64          2974 ns       2878 ns     247341  
BM_1F1<double>/32/4/-64          4416 ns       4310 ns     161223  
BM_1F1<double>/64/4/-64          6226 ns       5868 ns     121007  
BM_1F1<double>/-64/8/-64       406076 ns     391107 ns       1855  
BM_1F1<double>/1/8/-64           1766 ns       1733 ns     394415  
BM_1F1<double>/2/8/-64           1655 ns       1594 ns     451278  
BM_1F1<double>/4/8/-64           1734 ns       1624 ns     440193  
BM_1F1<double>/8/8/-64           2343 ns       2161 ns     303502  
BM_1F1<double>/16/8/-64          3478 ns       3361 ns     206629  
BM_1F1<double>/32/8/-64          4300 ns       4229 ns     168001  
BM_1F1<double>/64/8/-64          5610 ns       5516 ns     126943  
BM_1F1<double>/-64/16/-64      425990 ns     399085 ns       1785  
BM_1F1<double>/1/16/-64          4136 ns       2192 ns     317927  
BM_1F1<double>/2/16/-64          3436 ns       2233 ns     293680  
BM_1F1<double>/4/16/-64          4693 ns       2458 ns     274746  
BM_1F1<double>/8/16/-64          3831 ns       3179 ns     211311  
BM_1F1<double>/16/16/-64         4227 ns       3889 ns     162238  
BM_1F1<double>/32/16/-64         4942 ns       4511 ns     152863  
BM_1F1<double>/64/16/-64         6440 ns       5875 ns     121729  
BM_1F1<double>/-64/32/-64      580423 ns     481817 ns       1532  
BM_1F1<double>/1/32/-64          4978 ns       4336 ns     166567  
BM_1F1<double>/2/32/-64          6420 ns       6042 ns     113018  
BM_1F1<double>/4/32/-64          2726 ns       2486 ns     269551  
BM_1F1<double>/8/32/-64          3900 ns       3633 ns     196520  
BM_1F1<double>/16/32/-64         5416 ns       4389 ns     147648  
BM_1F1<double>/32/32/-64         5837 ns       5198 ns     141455  
BM_1F1<double>/64/32/-64         7725 ns       6815 ns     121003  
BM_1F1<double>/-64/64/-64      673155 ns     483674 ns       1446  
BM_1F1<double>/1/64/-64         15251 ns      12737 ns      59144  
BM_1F1<double>/2/64/-64         12570 ns       9853 ns      73765  
BM_1F1<double>/4/64/-64          3296 ns       3054 ns     238610  
BM_1F1<double>/8/64/-64          3846 ns       3608 ns     197320  
BM_1F1<double>/16/64/-64         4466 ns       4256 ns     171563  
BM_1F1<double>/32/64/-64         6484 ns       5618 ns     139334  
BM_1F1<double>/64/64/-64         7309 ns       6991 ns     111081  
BM_1F1<double>/-64/-64/1         6115 ns       5885 ns     120062  
BM_1F1<double>/1/-64/1           4064 ns       3807 ns     174121  
BM_1F1<double>/2/-64/1           3357 ns       3252 ns     222828  
BM_1F1<double>/4/-64/1           4093 ns       3672 ns     204435  
BM_1F1<double>/8/-64/1           4275 ns       4016 ns     178194  
BM_1F1<double>/16/-64/1         10241 ns       9880 ns      73451  
BM_1F1<double>/32/-64/1         15015 ns      14761 ns      44191  
BM_1F1<double>/64/-64/1          5260 ns       5162 ns     138433  
BM_1F1<double>/-64/1/1            951 ns        938 ns     752025  
BM_1F1<double>/1/1/1             1362 ns       1335 ns     543390  
BM_1F1<double>/2/1/1             1516 ns       1494 ns     466660  
BM_1F1<double>/4/1/1             2129 ns       2101 ns     334893  
BM_1F1<double>/8/1/1             1877 ns       1849 ns     379789  
BM_1F1<double>/16/1/1            2092 ns       2065 ns     336509  
BM_1F1<double>/32/1/1            2693 ns       2303 ns     330011  
BM_1F1<double>/64/1/1            1043 ns        925 ns     773165  
Assertion failed: (a_b_shift > 1), function hypergeometric_1F1_backwards_recursion_on_b_for_negative_a, file ./include/boost/math/special_functions/detail/hypergeometric_1F1_recurrence.hpp, line 238.  
```

---

## Comment 31

> Username: NAThompson  
> Created_at: 2019-02-08 05:28:39 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-461695154  

The assertion can be hit via the following inputs:  
  
```  
a_b_shift = 1  
(a,b,z) = (-2.61814565049903725, 1.0819652137298521, 64.6011091638099089)  
```  
  
Another failure case is:  
  
```  
a_b_shift = 0  
(a,b,z) = (-1.98018241448205767, 1.98450573845762079, 64.4977916804564302)  
```

---

## Comment 32

> Username: jzmaddock  
> Created_at: 2019-02-08 13:18:37 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-461799886  

Thanks Nick.  
  
I've just started random testing (as in "let's go and really look for issues"), and found a couple hundred in a few seconds of CPU time, so there's still plenty to do!  
  
Will add yours to the list.

---

## Comment 33

> Username: jzmaddock  
> Created_at: 2019-02-16 19:50:52 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-464377906  

>The assertion can be hit via the following inputs:  
  
I'm not able to reproduce, in particular those inputs should not be taking that routine (and don't for me).  
  
I have random tests running now so maybe that will show something...

---

## Comment 34

> Username: NAThompson  
> Created_at: 2019-02-16 20:01:04 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-464379032  

Strange; do you have [Google benchmark](https://github.com/google/benchmark) installed on your system? I pulled down your latest commits, but I'm still hitting that assert using the benchmark code provided above.

---

## Comment 35

> Username: jzmaddock  
> Created_at: 2019-02-16 21:30:34 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-464387663  

>Strange; do you have [Google benchmark](https://github.com/google/benchmark) installed on your system? I pulled down your latest commits, but I'm still hitting that assert using the benchmark code provided above.  
  
Not yet, I'll try and look at that tomorrow.  Your test cases BTW both go to hypergeometric_1F1_asym_large_z_series for me.

---

## Comment 36

> Username: jzmaddock  
> Created_at: 2019-02-17 07:46:24 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-464426024  

>Not yet, I'll try and look at that tomorrow. Your test cases BTW both go to hypergeometric_1F1_asym_large_z_series for me.  
  
Never mind I have a reproducer... looking at that now.

---

## Comment 37

> Username: jzmaddock  
> Created_at: 2019-02-17 10:02:17 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-464436953  

The recursion/assertion bug should be fixed now.  
  
More errors from random testing still showing up though...

---

## Comment 38

> Username: NAThompson  
> Created_at: 2019-02-17 20:00:17 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-464501723  

Just verified the fix; everything looks good now.  
  
I challenged it with a larger domain, and got the following error:  
  
```  
libc++abi.dylib: terminating with uncaught exception of type boost::wrapexcept<std::overflow_error>: Error in function boost::math::hypergeometric_1F1<double>(double,double,double): numeric overflow  
```  
  
Is it possible to include the inputs that caused the overflow in the error message, or have there been too many domain transforms to keep track of?

---

## Comment 39

> Username: jzmaddock  
> Created_at: 2019-02-18 11:51:51 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-464703573  

>Is it possible to include the inputs that caused the overflow in the error message, or have there been too many domain transforms to keep track of?  
  
That's actually a good idea for better error messages - I think I would need to update the whole error reporting mechanism though - let me think on that.  
  
But yes, overflows only happen at the very end, so we do know the actual arguments.   That's not true of other error conditions (evaluation_error's) though.  I'm hoping that means that we can provide things like log(1F1), regularised 1F1 (1F1(a,b,z)/tgamma(b)), and Whitaker functions as simple wrappers around the internals without spurious over/under flow.

---

## Comment 40

> Username: NAThompson  
> Created_at: 2019-02-20 20:03:27 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-465733029  

Do you mind doing a [CI SKIP] merge from develop on this? I've got all my benchmarks in a single file, so I have to comment them out whenever I run the hypergeometric benchmarks, and I've gotta comment out the hypergeometric benchmarks whenever I run anything else.

---

## Comment 41

> Username: jzmaddock  
> Created_at: 2019-02-20 20:16:16 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-465737252  

Not at all, I'm running the tests on develop at present for the Newton-Raphson bug fix, so it'll likely be tomorrow before I do that - unless you want to do it now?

---

## Comment 42

> Username: NAThompson  
> Created_at: 2019-02-20 20:18:30 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-465737942  

Nah, it's no rush; at your leisure. Just a minor pain point for me.

---

## Comment 43

> Username: NAThompson  
> Created_at: 2019-03-09 06:21:18 UTC  
> Updated_at: 2019-03-09 06:21:45 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-471150407  

Quick comment: The table below really gets mangled on Chrome, with the comment spacing way too wide. I have no clue how to fix it!  
  
  
<img width="1258" alt="Screen Shot 2019-03-08 at 11 20 02 PM" src="https://user-images.githubusercontent.com/5459618/54067102-ca1b6a80-41f8-11e9-80b1-bba13ad1417e.png">

---

## Comment 44

> Username: jzmaddock  
> Created_at: 2019-03-09 08:39:37 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-471158489  

>Quick comment: The table below really gets mangled on Chrome, with the comment spacing way too wide. I have no clue how to fix it!  
  
Good catch!  A non-breaking space should fix this but apparently doesn't do so :(  
  
Had to use SVG equations instead, so another <shrug> I guess.

---

## Comment 45

> Username: NAThompson  
> Created_at: 2019-03-09 17:48:39 UTC  
> Updated_at: 2019-03-09 17:55:07 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-471205392  

wowie! I now understand why you've left 2F1 for another day. 1F1 is a monster.  
  
one more comment on the docs: Did you need to print all the digits right hand column of the table? It's still generating weird breakpoints for me.  
  
Another question: Do you think there's a straightforward way to compute cond(1F1; a, b,z)? That might be very informative . . .

---

## Comment 46

> Username: jzmaddock  
> Created_at: 2019-03-09 19:08:15 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-471212553  

>wowie! I now understand why you've left 2F1 for another day. 1F1 is a monster.  
  
And I'm still finding bugs :(  
  
>one more comment on the docs: Did you need to print all the digits right hand column of the table? It's still generating weird breakpoints for me.  
  
It's not ideal for sure, I put all the digits in there in case someone wants to reproduce those test cases, can you think of a better way to format things?  
  
>Another question: Do you think there's a straightforward way to compute cond(1F1; a, b,z)? That might be very informative . . .  
  
I think with 3 degrees of freedom that is likely to be hard in general?  
  
The derivative on z is well known (https://dlmf.nist.gov/13.3#E15), the derivatives on a and b not so much, there's a link above some where I think...

---

## Comment 47

> Username: NAThompson  
> Created_at: 2019-03-09 20:28:04 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-471219212  

> It's not ideal for sure, I put all the digits in there in case someone wants to reproduce those test cases, can you think of a better way to format things?  
  
I see; I thought the point was to give a number which is "big" or "small" in this context. In which case many significant digits aren't too important.  
  
> The derivative on z is well known (https://dlmf.nist.gov/13.3#E15), the derivatives on a and b not so much, there's a link above some where I think...  
  
The analytic derivatives are hideous, omg, and probably more work than 1F1 itself:  
  
http://functions.wolfram.com/HypergeometricFunctions/Hypergeometric1F1/20/01/01/  
  
Maybe you can finite difference or autodiff into something acceptable with minimal effort, but near as I can tell, this was a super hard suggestion.

---

## Comment 48

> Username: jzmaddock  
> Created_at: 2019-03-11 09:12:58 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-471459923  

With regard to condition number, for x it's:  
  
```  
C = x M'[a,bx]/M[a,b,x] = xa/b * M[a+1,b+1,x]/M[a,b,x]  
```  
  
And for some domains, the ratio `M[a+1,b+1,x]/M[a,b,x]` is super easy to calculate: the recurrence relations for a+1,b+1 are minimal for M[a+N,b+N,x] as N->Infinity so backwards recursion is stable, and the associated continued fraction gives the ratio.  However as _a_ becomes small, and/or changes sign the direction of stability (if there is one at all) can change, and for `b < 0` it's all over the shop.  It might be possible to bracket the value and/or set an upper bound by calculating the ratio from both forward and backwards recurrences and assuming the larger of the 2 is the maximal solution.  I _think_ that would work, but I've outstretched my theory knowledge here.  
  
For sensitivity on a and b, one could estimate the derivative to be  
  
```  
M[a+1,b,x]/m[a,b,x]  
```  
and  
```  
M[a,b+1,x]/M[a,b,x]  
```  
  
But the same comments about stability of the recurrences apply.

---

## Comment 49

> Username: NAThompson  
> Created_at: 2019-03-11 14:50:01 UTC  
> Updated_at: 2019-03-11 14:50:56 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-471570595  

I'm wondering how good an estimate you're gonna get with the unit step though. I believe that the condition number goes infinite as b approaches a negative integer, so for (say) b=-1.01111 I feel like that approximation isn't going to tell the story you expect.  
  
For the x derivative it looks good, though the 'autodiff cheap gradient theorem' might be more useful here.

---

## Comment 50

> Username: NAThompson  
> Created_at: 2019-03-14 14:34:28 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-472888181  

BTW here's how to label the axes as a,b,z rather than x,y,z:  
  
https://plot.ly/javascript/axes/#set-and-style-axes-title-labels-and-ticks

---

## Comment 51

> Username: jzmaddock  
> Created_at: 2019-03-14 18:16:14 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-472997365  

>BTW here's how to label the axes as a,b,z rather than x,y,z:  
  
Oops, oversight on my part, corrected now.

---

## Comment 52

> Username: NAThompson  
> Created_at: 2019-03-15 23:59:22 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-473476351  

Your first graph, the blue histogram: The title is "Error rates in 1F1." But isn't the "x-axis" mislabelled? It is currently "Error rate in 2^N\epsilon", but should it be just "ULP error"?  
  
As for your 3D plotly.js graphics, why didn't you just sample the points from a uniform distribution? It looks like the current selection is not random, but not uniform either.

---

## Comment 53

> Username: jzmaddock  
> Created_at: 2019-03-16 08:49:17 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-473512615  

>Your first graph, the blue histogram: The title is "Error rates in 1F1." But isn't the "x-axis" mislabelled? It is currently "Error rate in 2^N\epsilon", but should it be just "ULP error"?  
  
It is quite literally in units of 2^N eps, which is the same as the number of bits that are incorrect, but I'm not sure that's quite the same as ULP?  
  
>As for your 3D plotly.js graphics, why didn't you just sample the points from a uniform distribution? It looks like the current selection is not random, but not uniform either.  
  
I ran 100000 uniformly distributed random points - but you can't put that many on the graph or it just looks like a solid lump - even if you turn the opacity right down.  It also crashed my browser when I tried to do that :(   So... the graph just shows the outliers, the very worst errors that were found.  It does mention that in the text but probably not strongly enough.  I'll add something to the legend as well "< 256 not shown" or whatever for folks that just look at the pictures (been there done that!)   ;)

---

## Comment 54

> Username: jzmaddock  
> Created_at: 2019-03-16 10:31:29 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-473518843  

See how the graphs look now - I renamed some files for consistency, so you may need to rebuild the docs.  
On the bar chart axis: I'm open to suggestions.  It's basically a logarithmic x axis, but OpenOffice wouldn't play nice with that without mangling the legends really quite badly, likewise Paul's SVGPlot.  I can do prettier in plot.ly but then we don't get svg's :(

---

## Comment 55

> Username: NAThompson  
> Created_at: 2019-03-16 13:52:03 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-473532051  

> It is quite literally in units of 2^N eps, which is the same as the number of bits that are incorrect, but I'm not sure that's quite the same as ULP?  
  
It's not the same as ULP distance; I've just never seen that notation before. I guess 2^3 eps = 8 ULP. Actually very intuitive once I figured your meaning; it's kinda a log_2(ULP).  
  
The "<256 not shown for brevity" makes the graph very readable now.  
  
When I rebuild the docs I get:  
  
```  
warning: failed to load external entity "../boost/libs/math/doc/graphs/hypergeometric_1f1/positive_abz.html"  
ERROR: dbhtml-include processing instruction href has no content.  
```

---

## Comment 56

> Username: jzmaddock  
> Created_at: 2019-03-16 17:00:16 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-473565209  

>warning: failed to load external entity "../boost/libs/math/doc/graphs/hypergeometric_1f1/positive_abz.html"  
>ERROR: dbhtml-include processing instruction href has no content.  
  
Ugh, I missed committing a couple of files, fixed now.

---

## Comment 57

> Username: NAThompson  
> Created_at: 2019-03-17 00:41:22 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-473604782  

Sadly, now my plotly.js graphic has evaporated; here's the firefox console:  
  
<img width="1277" alt="Screen Shot 2019-03-16 at 8 39 05 PM" src="https://user-images.githubusercontent.com/5459618/54483572-ab862680-482b-11e9-9b99-53797aae9ee3.png">  
  
  
  
On your first histogram, it looks like the error decays exponentially or as a power law. Why isn't the 0 one of the bins? Shouldn't it be the tallest bin?  
  
Also, instead of "Error rate in 2^N\epsilon$, wouldn't "Number of incorrect bits" be more clear?

---

## Comment 58

> Username: jzmaddock  
> Created_at: 2019-03-17 08:45:34 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-473636396  

>Sadly, now my plotly.js graphic has evaporated; here's the firefox console:  
  
That's strange - works for me.  Note that I changed the code to only load the plotly javascript on the first graph, second graph assumes it's already loaded, is that the full docs you have there or a bit extracted out?  Just checking 'cos I can't think of anything else!  
  
>On your first histogram, it looks like the error decays exponentially or as a power law. Why isn't the 0 one of the bins? Shouldn't it be the tallest bin?  
  
It is - the error rate shown is the maximum of each range, so the first bin is 0 <= err <= 1, the second 1 < err <= 2 and so on.  
  
>Also, instead of "Error rate in 2^N\epsilon$, wouldn't "Number of incorrect bits" be more clear?  
  
Might be, it just seemed a strange measure somehow, as I said I really just wanted a logarithmic axis...

---

## Comment 59

> Username: NAThompson  
> Created_at: 2019-03-17 14:26:11 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-473670662  

> That's strange - works for me.   
  
Well, all that matters is that is works coming off the boost server. So I say this looks good!

---

## Comment 60

> Username: jzmaddock  
> Created_at: 2019-03-17 16:52:21 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-473683768  

>Well, all that matters is that is works coming off the boost server. So I say this looks good!  
  
Needs to work on the users hard disk as well, but yes we're getting there I think.  
  
Meanwhile... trying to improve the b < 0 case, it would help somewhat to more rigorously map out the domains where each method is viable, it's easy enough (albeit with several days of CPU time) to create a 3D grid of points that map out each boundary, but then we have the problem of interpolating between them.  I thought your CatmulRom code might do it, but it appears not?  Is this something that might interest you?  No particular hurry, could be left to a future version.

---

## Comment 61

> Username: NAThompson  
> Created_at: 2019-03-18 02:23:00 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-473746421  

Sadly Catmull-Rom won't work here since it places a 1D arc through an nD space. I believe you want to embed a 2D surface in a 3D space. Actually I think Catmull worked on this problem as well but I never read his papers on it.

---

## Comment 62

> Username: NAThompson  
> Created_at: 2019-03-18 02:24:05 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-473746591  

Or were you thinking of a tricubic b-spline?

---

## Comment 63

> Username: jzmaddock  
> Created_at: 2019-03-18 10:52:53 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-473861966  

To be honest, I think I was greatly over-thinking this!  
  
Currently my - rather braindead and in need of improvement code - does something like "largest neighbour" which is the safest option in that particular context, but can be rather over-conservative.  A simple bilinear interpolation would probably do the trick as the surfaces marking out the domains of each method don't seem to be highly curved (so far).  I'm finding other domains where my existing selection heuristics aren't up to the job, so I'll give that a go and see.

---

## Comment 64

> Username: NAThompson  
> Created_at: 2019-03-18 15:27:30 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-473959541  

Right, unless you have some reason to believe there's some smoothness to your field, there's no reason to think that fancy Ck interpolation will outperform linear.  
  
My worry is that if you have n methods of computation, and you have to partition 3d space into regions where each method best you have a lot of compute to do to define those regions and then you have to store those regions in some efficient data structure. It seems like a very subtle problem, probably publishable once solved.

---

## Comment 65

> Username: jzmaddock  
> Created_at: 2019-07-17 17:27:36 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-512440371  

TaDa!  
  
Not withstanding the fact that CI may turn into a bloodbath, and there are a couple of things that would be nice to look at again.... this is probably about done.  Probably ;)

---

## Comment 66

> Username: NAThompson  
> Created_at: 2019-07-17 17:33:39 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-512454887  

I just got the following error:  
  
```  
gcc.compile.c++ ../../../bin.v2/libs/math/test/test_1F1_log_dec_40.test/gcc-7/release/link-static/threading-multi/visibility-hidden/test_1F1_log.o  
In file included from test_1F1_log.hpp:25:0,  
                 from test_1F1_log.cpp:6:  
../../../boost/math/special_functions/hypergeometric_1F1.hpp:27:10: fatal error: boost/math/special_functions/detail/hypergeometric_1F1_negative_b_regions.hpp: No such file or directory  
 #include <boost/math/special_functions/detail/hypergeometric_1F1_negative_b_regions.hpp>  
          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
compilation terminated.  
  
```  
  
Presumably this is just a stray include?

---

## Comment 67

> Username: jzmaddock  
> Created_at: 2019-07-17 17:59:40 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-512503440  

Oh :(  
  
The file is present, but with a lowercase "f", I've tried renaming it on windows, but it doesn't work :(  
  
Can you rename?

---

## Comment 68

> Username: jzmaddock  
> Created_at: 2019-07-17 18:00:14 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-512503637  

I'm going to cancel the CI builds as well until the file name is fixed.

---

## Comment 69

> Username: NAThompson  
> Created_at: 2019-07-17 19:58:39 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-512545839  

> Can you rename?  
  
Done.  
  
BTW, one other issue: Really long compile times:  
  
```  
/boost/libs/math/test$ time ../../../b2 cxxflags="--std=c++14 -fsanitize=address -fsanitize=undefined" linkflags="-fsanitize=address -fsanitize=undefined" test_1F1_log_dec_40  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes (cached)  
    - Boost.Config Feature Check: cxx11_lambdas : yes (cached)  
    - Boost.Config Feature Check: cxx11_unified_initialization_syntax : yes (cached)  
    - Boost.Config Feature Check: cxx11_smart_ptr : yes (cached)  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes (cached)  
    - Boost.Config Feature Check: cxx11_lambdas : yes (cached)  
    - Boost.Config Feature Check: cxx11_unified_initialization_syntax : yes (cached)  
    - Boost.Config Feature Check: cxx11_smart_ptr : yes (cached)  
    - has_icu builds           : yes (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : yes (cached)  
...patience...  
...patience...  
```  
  
This has been compiling for 10 minutes without completion.  
  
```  
g++ --version  
g++ (Ubuntu 7.4.0-1ubuntu1~18.04.1) 7.4.0  
Copyright (C) 2017 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```

---

## Comment 70

> Username: jzmaddock  
> Created_at: 2019-07-18 08:34:51 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-512720859  

> BTW, one other issue: Really long compile times:  
  
Hmm, seems to be OK on windows and ubuntu (just double checking that now).  Can I get you to investigate some more?  What it does do I know is chew up a whole mass of memory - if it's launched a bunch of builds in parallel, could it be thrashing the swap space?

---

## Comment 71

> Username: NAThompson  
> Created_at: 2019-07-18 11:55:22 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-512784793  

It appears only to be a problem with the sanitizers. Running the whole thing without sanitizers gives me the following:  
  
```  
**passed** ../../../bin.v2/libs/math/test/test_1F1_log_dec_40.test/gcc-7/release/link-static/threading-multi/visibility-hidden/test_1F1_log_dec_40.test  
...updated 4 targets...  
  
real	3m47.204s  
user	3m46.203s  
sys	0m0.983s  
```  
  
Here's a `top` sample:  
  
```  
PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                                    
20   0   46280   6840   5888 R  94.1  0.0   2:25.46 test_1F1_log_de  
```  
  
Which doesn't look too bad. The following shows the perf profile of the entire compile + run   
  
![hypergeometric_perf](https://user-images.githubusercontent.com/5459618/61455240-faa2ac00-a930-11e9-94cb-1e5d5d00a9ac.png)  
  
Do you know about the `__udivti3`?

---

## Comment 72

> Username: NAThompson  
> Created_at: 2019-07-18 12:21:17 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-512792219  

Just a quick comment on your documentation for `pFq`: You write that you can return a norm, but the parameter is called `pNorm`. Might it be more understandable to name the parameter `l1norm` and specify that it is a summation norm?

---

## Comment 73

> Username: NAThompson  
> Created_at: 2019-07-18 12:44:33 UTC  
> Updated_at: 2019-07-18 12:45:22 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-512799328  

Probably bikeshedding, but I get a few warning here:  
  
```  
./test_1F1.hpp:251:58: error: non-constant-expression cannot be narrowed from type 'double' to 'float' in initializer list [-Wc++11-narrowing]  
         {{ std::ldexp((double)-15569844699136000, -52), std::ldexp((double)12855440629760000, -44), std::ldexp((double)12563412279296000, -45), SC_(0.097879401070280078654536987721507669872679020399179) }},  
                                                         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./test_1F1.hpp:251:58: note: insert an explicit cast to silence this issue  
         {{ std::ldexp((double)-15569844699136000, -52), std::ldexp((double)12855440629760000, -44), std::ldexp((double)12563412279296000, -45), SC_(0.097879401070280078654536987721507669872679020399179) }},  
                                                         ^~~~~~~~~~~~~~~~~  
```  
  
Invocation:  
  
```  
math/test$ ../../../b2 toolset=clang -j16 cxxflags="-g --std=c++14 -fsanitize=address -fsanitize=undefined" linkflags="-fsanitize=address -fsanitize=undefined"  
```

---

## Comment 74

> Username: NAThompson  
> Created_at: 2019-07-18 12:45:09 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-512799512  

And one error:  
  
```  
In file included from test_pFq.cpp:6:  
./test_pFq.hpp:238:10: fatal error: 'hypergeometric_2F1.ipp' file not found  
#include "hypergeometric_2F1.ipp"  
         ^~~~~~~~~~~~~~~~~~~~~~~~  
1 warning and 1 error generated.  
```

---

## Comment 75

> Username: jzmaddock  
> Created_at: 2019-07-18 17:39:09 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-512915843  

> Do you know about the __udivti3?  
  
No, looks like something gcc uses for simulating division?  
  
> Probably bikeshedding, but I get a few warning here:  
  
Testing local fixes now.  
  
> And one error:  
  
Also testing local fixes - lots of files with lower case "f"'s.

---

## Comment 76

> Username: jzmaddock  
> Created_at: 2019-07-18 18:23:19 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-512931305  

> Just a quick comment on your documentation for pFq: You write that you can return a norm, but the parameter is called pNorm. Might it be more understandable to name the parameter l1norm and specify that it is a summation norm?  
  
Nod.  The wording is confusing, I'll look at it again.  
  
I'm wondering though whether I need to rethink this - this started out as a straight absolute sum, but has morphed into somewhat more of an error estimate, because when the summation skips forward to handle series that have multiple peaks in them, it adds something to the norm to account for the error inherent in calculating a specific term via logs.  So... would it be better to change this to return an estimate of the absolute error (and name it as such) instead?  It still wouldn't include the error from summing N terms (potentially N/2 eps) as my gut says that would grossly over-inflate the error estimate.  but might be a more honest description?

---

## Comment 77

> Username: NAThompson  
> Created_at: 2019-07-18 19:20:18 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-512951659  

>  So... would it be better to change this to return an estimate of the absolute error (and name it as such) instead?   
  
Given your description, it does sound reasonable to rescale via the unit roundoff and use it as an absolute error estimate. In fact I was somewhat surprised to see the condition number of summation here because I didn't think it would be super relevant, and as you have found, other more specialized measures are more useful.

---

## Comment 78

> Username: NAThompson  
> Created_at: 2019-07-18 22:55:22 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-513023232  

Quick housekeeping:  
  
```  
test_1F0.cpp: In function ‘void expected_results()’:  
test_1F0.cpp:17:16: warning: variable ‘largest_type’ set but not used [-Wunused-but-set-variable]  
    const char* largest_type;  
                ^~~~~~~~~~~~  
```  
  
I'll fix if you don't have a commit you are gonna push.

---

## Comment 79

> Username: NAThompson  
> Created_at: 2019-07-21 15:09:22 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-513562447  

Looks like [this](https://ci.appveyor.com/project/jzmaddock/math/builds/26082962/job/if9t2sodo90nu2sf) build dies because of:  
  
```  
C:\Users\appveyor\AppData\Local\Temp\1\ccVRM3KD.s: Fatal error: can't write 303 bytes to section .text of ..\..\..\bin.v2\libs\math\test\tanh_sinh_quadrature_test_1a.test\d43c6768c960202759d81fa7e5e89dae\tanh_sinh_quadrature_test.o because: 'File too big'  
C:/mingw-w64/x86_64-6.3.0-posix-seh-rt_v5-rev1/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/6.3.0/../../../../x86_64-w64-mingw32/bin/as.exe: ..\..\..\bin.v2\libs\math\test\tanh_sinh_quadrature_test_1a.test\d43c6768c960202759d81fa7e5e89dae\tanh_sinh_quadrature_test.o: too many sections (33271)  
C:\Users\appveyor\AppData\Local\Temp\1\ccVRM3KD.s: Fatal error: can't close ..\..\..\bin.v2\libs\math\test\tanh_sinh_quadrature_test_1a.test\d43c6768c960202759d81fa7e5e89dae\tanh_sinh_quadrature_test.o: File too big  
```  
  
Maybe we were a bit too ambitious in testing that; think we should remove some of the multiprecision and maybe the long double tests?

---

## Comment 80

> Username: pabristow  
> Created_at: 2019-07-21 16:46:40 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-513569642  

I agree - we need to set up so that we can switch to one-time testing and/or local only testing in the jamfiles.  Perhaps John can advise the **best** way to do this?

---

## Comment 81

> Username: jzmaddock  
> Created_at: 2019-07-22 16:24:35 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-513854580  

Best way?  Not sure about that ;)  
  
We could mark long running tests as explicit and then provide an alias that builds them all, the trick is remembering to do so once in a while!  
  
But I agree we need to do something.

---

## Comment 82

> Username: jzmaddock  
> Created_at: 2019-07-22 17:26:45 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-513877442  

OK how about this: currently the tests are split up (In the Jamfile) into various test suites which are then invoked explicitly/separately from the CI scripts.  So how about we define a test suite "long-running-tests" which is not invoked from any CI script, but is otherwise run by default if you just "build everything" in the Jamfile (as in run locally).  Thoughts?

---

## Comment 83

> Username: NAThompson  
> Created_at: 2019-07-22 20:37:51 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-513944665  

> Thoughts?  
  
It's a good idea, but I have two alternatives:   
  
- Switch the tests over to the shared library version of Boost.Test. I found this reduces compile times a lot.  
- Switch the tests over to "math_unit_test.hpp", after adding `CHECK_ABSOLUTE_CLOSE` and `CHECK_RELATIVE_CLOSE`. This would reduce compile times even more.  
  
Both are more work than John's solution so are probably less helpful, but at the same time we don't lose the tests.

---

## Comment 84

> Username: jzmaddock  
> Created_at: 2019-07-23 07:24:05 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-514087422  

>It's a good idea, but I have two alternatives:  
>  
>    Switch the tests over to the shared library version of Boost.Test. I found this reduces compile times a lot.  
>    Switch the tests over to "math_unit_test.hpp", after adding CHECK_ABSOLUTE_CLOSE and CHECK_RELATIVE_CLOSE. This would reduce compile times even more.  
>  
>Both are more work than John's solution so are probably less helpful, but at the same time we don't lose the tests.  
  
Nod, both good ideas.  However, the multiprecision tests do take up a lot of runtime as well as compile time, but yes, compile time is a big issue.

---

## Comment 85

> Username: pabristow  
> Created_at: 2019-07-23 13:45:47 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-514216740  

Splitting into more than one test suite sounds neat, but would we not want to be able to trigger the testers to run the long-runners suite (as well as running locally) from time to time?  
  
[CI LONG_RUNNERS]    
or  
[CI MY_TEST_SUITE, MY_3RD_TEST_SUITE]  
   ??

---

## Comment 86

> Username: jzmaddock  
> Created_at: 2019-08-03 09:13:28 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-517908895  

Can I get you guys to take a look at this now?  I realise it's become a monster PR, but it is what it is I'm afraid (a monster function!).  
  
Anyhow CI is now all green, and it's functional enough to be useful IMO.

---

## Comment 87

> Username: NAThompson  
> Created_at: 2019-08-03 20:09:27 UTC  
> Updated_at: 2019-08-03 20:10:11 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-517951259  

I just read through the docs: The graphs are showing up and are comprehensible. The only thing I see is that on the 1F1 doc page, the "log" version shows "ln" italicized, rather than Roman. Also, not italicized 'F's in 1F1 in this sentence:  
> This transition is moderated not by a change of sign in the recurrence coefficients themselves, but by a change in the behaviour of the values of 1F1 - from alternating in sign when |b| is small to having the same sign when b is larger. During the actual transition, 1F1 will either pass through a zero or a minima depending on whether b is even or odd (if there is a minima at 1F1(a, b, z) then there is necessarily a zero at 1F1(a+1, b+1, z) as per the derivative of the function).   
  
>  For both a and b less than zero, the errors the worst errors are clustered in a "difficult zone" with b < a and z ≅ a:  
  
I read "z ≅ a" as z is congruent to a, not z is approximately a. That's probably bikeshedding. . .   
  
I have ran as many unit tests as I could under `-fsanitize=address -fsanitize=undefined` and no issues popped up.  
  
I ran `cppcheck` on all the hypergeometric files. No errors were found, but a couple of cleanliness issues:  
  
```  
$  cppcheck --enable=all --inconclusive  -I../../.. hypergeometric_1F0.hpp  
[../../../boost/math/special_functions/detail/hypergeometric_series.hpp:340] -> [../../../boost/math/special_functions/detail/hypergeometric_series.hpp:365]: (style) Variable 'term_m1' is reassigned a value before the old one has been used.  
[../../../boost/math/special_functions/detail/hypergeometric_1F1_bessel.hpp:140] -> [../../../boost/math/special_functions/detail/hypergeometric_1F1_bessel.hpp:187]: (style) Local variable 'scale' shadows outer function  
[../../../boost/math/special_functions/detail/hypergeometric_1F1_bessel.hpp:140] -> [../../../boost/math/special_functions/detail/hypergeometric_1F1_bessel.hpp:262]: (style) Local variable 'scale' shadows outer function  
[../../../boost/math/special_functions/math_fwd.hpp:894] -> [hypergeometric_1F1.hpp:637]: (style) Local variable 'sign' shadows outer function  
[../../../boost/math/special_functions/detail/hypergeometric_series.hpp:392]: (style) Variable 'local_scaling' is assigned a value that is never used.  
[../../../boost/math/special_functions/detail/hypergeometric_series.hpp:399]: (style) Variable 'local_scaling' is assigned a value that is never used.  
[../../../boost/math/tools/roots.hpp:194]: (style) Variable 'fmax' is assigned a value that is never used.  
[../../../boost/math/special_functions/detail/hypergeometric_rational.hpp:47]: (style) Variable 'a4' is assigned a value that is never used.  
[../../../boost/math/special_functions/detail/hypergeometric_rational.hpp:47]: (style) Variable 'b4' is assigned a value that is never used.  
[../../../boost/math/special_functions/detail/hypergeometric_rational.hpp:48]: (style) Variable 'prev_result' is assigned a value that is never used.  
[../../../boost/math/tools/recurrence.hpp:148]: (style) Variable 'third' is assigned a value that is never used.  
[../../../boost/math/tools/recurrence.hpp:203]: (style) Variable 'next' is assigned a value that is never used.  
[../../../boost/math/special_functions/detail/hypergeometric_pFq_checked_series.hpp:132]: (style) Variable 'term_m1' is assigned a value that is never used.  
[../../../boost/math/special_functions/detail/hypergeometric_pade.hpp:40]: (style) Variable 'prev_result' is assigned a value that is never used.  
[../../../boost/math/special_functions/detail/hypergeometric_pade.hpp:96]: (style) Variable 'prev_result' is assigned a value that is never used.  
```  
  
None of this stuff affects the operation of the code at all, so if you don't mind I will merge this so I can start using it for the Jacobi polynomials without branching off this branch.

---

## Comment 88

> Username: jzmaddock  
> Created_at: 2019-08-03 20:56:17 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-517954123  

OK, I went ahead and merged.  I'll take care of the cosmetic stuff later.  
  
With regard to the Jacobi polynomials, only thing you'll have to check is the stability of the recurrence relations for various values of the arguments... hopefully it won't get too complex but you never know.

---

## Comment 89

> Username: NAThompson  
> Created_at: 2019-08-03 22:00:33 UTC  
> Updated_at: 2019-08-03 22:01:04 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-517957500  

Yeah, regrettably I just found a paper which claims an improvement on the Clenshaw recurrence:  
  
https://arxiv.org/pdf/1602.02618.pdf  
  
They call it "Reinsch’s modification", and now I feel honor bound to examine it before dropping it into boost.math. So more time as always. . . .

---

## Comment 90

> Username: pabristow  
> Created_at: 2019-08-07 14:47:57 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-519130620  

From: jzmaddock <notifications@github.com>   
Sent: 3 August 2019 10:13  
To: boostorg/math <math@noreply.github.com>  
Cc: Paul A. Bristow <paul@pbristow.uk>; Comment <comment@noreply.github.com>  
Subject: Re: [boostorg/math] Hypergeometrics (#148)  
  
   
  
Can I get you guys to take a look at this now? I realise it's become a monster PR, but it is what it is I'm afraid (a monster function!).  
  
Looks like Mission Impossible!    
  
I could only find the odd i to dot, but a couple of random observations:  
  
1.	You have successfully thwarted my indenting scheme but putting equations in a hypergeometric subfolder 😉  
  
[$../equations/hypergeometric_1f1_17.svg]  doesn’t use the template [equation hypergeometric_1f1_17] for no doubt some good reason.  
  
The [: ] that I added is now not honoured.  But not a showstopper.  
  
2  As usual the graphics cause trouble.  
  
ON PDF Hypergeometric 1F1  (a,b,z) heading incalculable points for b <0 is truncated at ‘being rais’  
  
But the 3D plot is OK (-ish)  
  
On html it isn’t visible at all.  
  
file:///I:/boost/libs/math/doc/graphs/hypergeometric_1f1/negative_b_incalculable.html  
  
contains from html  JavaScript – that I presume must be part of a page to run  
  
I have JavaScript I think, but not plot.ly  
  
I’ve tried building with [def __build_html ]  to define the macro so the condition is true.  Should this be necessary?  
  
https://www.boost.org/doc/libs/1_70_0/doc/html/quickbook/syntax/phrase.html#quickbook.syntax.phrase.cond  
  
__base_path__ should be defined as ? and where?  
  
What am I doing wrong?  
  
Ah –  I am using IE11 and that is blocking Javascripts  
  
Internet Explorer restricted running active scripts…    I’ve clicked ‘Allow blocked content’ – but nothing shows.  
  
I have changed some settings – will see what happens after a windows restart.  
  
But Chrom works as expected 😊  Also Firefox and Edge are OK.  What are Microsoft doing?  
  
But in the end nothing beats a .png or .svg?  
  
I am not entirely convinced that it is necessary to have 100000 scatter points to show the ‘incalculable’ zones?  
  
If you had 5000 or 10000 would it not be viewable but still show the key feature?   
  
I’m sure you will be glad to declare it done for now and move on ?  
  
A few typos pushed     To github.com:boostorg/math.git       d26d90d7b..44e3ed82f  develop -> develop

---

## Comment 91

> Username: jzmaddock  
> Created_at: 2019-08-07 17:36:36 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-519197109  

>[$../equations/hypergeometric_1f1_17.svg]  doesn’t use the template [equation hypergeometric_1f1_17] for no doubt some good reason.  
  
Nod.  I switched to an online Latex editor for these so there is only SVG, no and MathML at present (not that we're using it yet!).  Sorry about the indents, I'll add those later if you haven't already.  
  
>ON PDF Hypergeometric 1F1  (a,b,z) heading incalculable points for b <0 is truncated at ‘being rais’  
  
Ugh.  I only have png's for those which isn't ideal (see below).  
  
> Ah –  I am using IE11 and that is blocking Javascripts  
  
OK, I see that as well - though IE does pop up a message which lets you run the script.  Microsoft Edge does show them up OK though.  
  
>But in the end nothing beats a .png or .svg?  
  
I would render them as SVG if I could, but have no means of doing so :(  
  
> I am not entirely convinced that it is necessary to have 100000 scatter points to show the ‘incalculable’ zones?  
  
There aren't anything like that many points in the graphs - just a couple hundred of the "worst" points.  I did try to display the full data set at one point and it brought my whole machine down!  
  
>I’m sure you will be glad to declare it done for now and move on ?  
  
Yes please ! ;)

---

## Comment 92

> Username: pabristow  
> Created_at: 2019-08-08 07:57:12 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-519411569  

Mission Impossible accepted - and, like Tom Cruise, job done!

---

## Comment 93

> Username: ckormanyos  
> Created_at: 2019-08-08 20:46:51 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-519681307  

It is great. Thank you. It's amazing what you guys have done with this.  
  
I'm confused about the logic in cyl_bessel_i_shrinkage_rate in 1F1. If z is 5, I return 1, but don't I want 0.25? It almost seems like the order of the if and else statements might be backward?

---

## Comment 94

> Username: jzmaddock  
> Created_at: 2019-08-09 17:49:51 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-520007263  

>I'm confused about the logic in cyl_bessel_i_shrinkage_rate in 1F1. If z is 5, I return 1, but don't I want 0.25? It almost seems like the order of the if and else statements might be backward?  
  
Ahhh!  Yes, the first conditional should be `if (z > 160)`, testing locally now....

---

## Comment 95

> Username: jzmaddock  
> Created_at: 2019-08-11 11:46:39 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-520221685  

>Ahhh! Yes, the first conditional should be if (z > 160), testing locally now....  
  
My bad, that's clearly wrong too: since this only applies for z < 0, the test values should all be negative.  Fixed and applied to develop.  
  
Aside: I really hate issues like this - they don't effect correctness as such, just performance and/or accuracy, which makes them really hard to test for :(

---

## Comment 96

> Username: NAThompson  
> Created_at: 2019-08-11 14:12:25 UTC  
> Url: https://github.com/boostorg/math/pull/148#issuecomment-520231571  

> Aside: I really hate issues like this - they don't effect correctness as such, just performance and/or accuracy, which makes them really hard to test for :(  
  
You have hypergeometric PTSD! And I'm over here picking the low hanging fruit like the cardinal B-splines, which took 100 LOC!

---
