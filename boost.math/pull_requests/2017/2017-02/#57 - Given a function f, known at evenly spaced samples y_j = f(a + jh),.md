# #57 Given a function f, known at evenly spaced samples y_j = f(a + jh), [Merged]

> Username: NAThompson  
> Created at: 2017-02-24 00:22:50 UTC  
> Updated at: 2017-05-03 12:34:18 UTC  
> Merged at: 2017-05-03 12:34:18 UTC  
> Closed at: 2017-05-03 12:34:18 UTC  
> Url: https://github.com/boostorg/math/pull/57  

this function constructs an interpolant using compactly supported cubic b splines.  
The advantage of using splines of compact support over traditional cubic splines  
is that compact support makes the splines well-conditioned.  
  
The interpolant is constructed in O(N) time and can be evaluated in constant time.  
Its error is O(h^4), and obeys the interpolating condition s(x_j) = f(x_j) for all samples.  
In addition, f' can be estimated from s', albeit with lower accuracy.  
  
This routine is cppcheck clean, and is clean under AddressSanitizer and MemorySanitizer.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2017-02-24 00:30:40 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-282166876  

Ok guys, we've already discussed that boost/math isn't really the right place for this function, but I think nonetheless there's been a number of improvements that will let us have a more productive discussion about whether this is something that should go into boost.  
  
First off, it now works with the multiprecision float128 type. I couldn't get it to work with the other multiprecision backends, but presumably there's only some moderate effort there.  
  
Next, I've added unit tests and documentation. From these tests I found that the main contribution to the loss of precision is the one-sided derivative estimation, so if anyone want to look at that and see if they can get a few more digits there's definitely room for improvement there. How much it would matter on real data is not evident to me, but it's still best to not let abstractions leak if you can.  
  
The tests run on random data, and sometimes fail. I'm not recommending random unit tests for permanent use, but in fact the failures are very indicative of the power and limitations of the method. So see if the errors that are being spat out are acceptable (I think they are) and if so we can move forward.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-03-02 10:33:30 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-283617208  

Nick, I think this looks promising, and I see no real reason not to host it as part of Boost.Math.  I have a few questions and nit-picking comments that I'll annotate on the code.....

---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2017-03-02 10:39:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/57#pullrequestreview-24698786  

📁 doc/interpolators/cubic_b_spline.qbk

```diff
  17 |+ Since most ODE steppers are adaptive, they must be able to sample the coefficients at arbitrary points; not just at the points we know the values of our function.
  18 |+ 
  19 |+ The routine must be provided the following arguments:
```

> Username: jzmaddock  
> Created_at: 2017-03-02 10:39:00 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103894845  

Nit: it's not a routine, it's a class.  I had to look at the code to understand that, as this part of the docs didn't make sense to me.  So you need to document the class along with it's member functions.  
  
Also this "looks like" a function object to me, is it worth overloading operator() ?  One possible objection would be that copying the object is expensive and function objects are generally passed around by value.  However, converting the internals to the PIMPL idiom would quickly solve that especially as there are no mutating member functions.

> Username: NAThompson  
> Created_at: 2017-03-02 17:58:46 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103988612  

Docs fixed. Overloading operator() is a big improvement to usability and readability.  
  
I didn't implement PIMPL, though I don't object to that. Copying the object is definitely expensive, but is there a use case? Sharing the function pointer to the overloaded () or the `prime` function seems more reasonable.


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2017-03-02 10:41:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/57#pullrequestreview-24699322  

📁 include/boost/math/interpolators/cubic_b_spline.hpp

```diff
  32 |+ 
  33 |+ using boost::math::constants::third;
  34 |+ using boost::math::constants::half;
```

> Username: jzmaddock  
> Created_at: 2017-03-02 10:41:40 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103895295  

I would prefer not to have these at namespace scope if possible as users writing "using namespace boost::math" get (a little) more than expected.  Actually I see no reason not to add 1/6 to Math.Constants if we need it here?

> Username: pabristow  
> Created_at: 2017-03-02 11:51:23 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103906958  

Coincidentally I've also got a need for a 1/6 (and a few others) so I will add to constants in due course.

> Username: NAThompson  
> Created_at: 2017-03-02 18:01:19 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103989151  

Fixed. Also added 1/6 to boost::math::constants.


---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2017-03-02 10:44:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/57#pullrequestreview-24699736  

📁 include/boost/math/interpolators/cubic_b_spline.hpp

```diff
  37 |+ Real b3_spline(Real x)
  38 |+ {
  39 |+     auto sixth = half<Real>()*third<Real>();
```

> Username: jzmaddock  
> Created_at: 2017-03-02 10:44:01 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103895686  

I realise use of auto makes the code quicker to write, but is there any reason not to just search and replace for "auto->Real" and have the code work with C++03 compilers as well?

> Username: pabristow  
> Created_at: 2017-03-02 11:53:14 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103907271  

It would be more consistent with other Boost.Math code to use RealType (even if in retrospect Real might be better - RealType is perhaps a tad 'Hungarian' ;-) )

> Username: NAThompson  
> Created_at: 2017-03-02 18:01:36 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103989202  

Removed the auto; there is no need for it.


---

## Review 6 [Commented]

> Username: jzmaddock  
> Created_at: 2017-03-02 10:45:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/57#pullrequestreview-24700088  

📁 include/boost/math/interpolators/cubic_b_spline.hpp

```diff
 112 |+     }
 113 |+ 
 114 |+     if (boost::math::isnan(left_endpoint))
```

> Username: jzmaddock  
> Created_at: 2017-03-02 10:45:55 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103896024  

Question: what happens if the endpoints are infinite?  Do we need to add tests for this?

> Username: NAThompson  
> Created_at: 2017-03-02 18:00:49 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103989043  

Added a check to see that the right endpoint doesn't overflow the requested type. If the left endpoint `= -std::numeric_limits<Real>::max()`, there should be no problem.


---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2017-03-02 10:52:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/57#pullrequestreview-24701412  

📁 test/test_cubic_b_spline.cpp

```diff
  55 |+ {
  56 |+     std::cout << "Testing interpolation condition for cubic b splines on type " << boost::typeindex::type_id<Real>().pretty_name()  << "\n";
  57 |+     std::random_device rd;
```

> Username: jzmaddock  
> Created_at: 2017-03-02 10:52:40 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103897197  

As noted in your comments, longer term this is probably not a good test, apart from anything else, random_device is only parted supported on some OS's and in any case gives different results on each run.

> Username: NAThompson  
> Created_at: 2017-03-02 18:03:55 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103989649  

I removed most of the randomness from this test, however I think that testing the interpolation condition with random points is a very strong one. I've reduced the precision so that I couldn't get any failures on many runs.


---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2017-03-02 10:53:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/57#pullrequestreview-24701577  

📁 test/test_cubic_b_spline.cpp

```diff
 100 |+     for (size_t i = 0; i < v.size(); ++i)
 101 |+     {
 102 |+         auto y = spline.interpolate_at(i*step + a);
```

> Username: jzmaddock  
> Created_at: 2017-03-02 10:53:31 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103897342  

We can also test in-between the control points since we know what the value is there?

> Username: NAThompson  
> Created_at: 2017-03-02 18:04:16 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103989733  

This was a mistake; fixed now.


---

## Review 9 [Commented]

> Username: jzmaddock  
> Created_at: 2017-03-02 10:53:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/57#pullrequestreview-24701637  

📁 test/test_cubic_b_spline.cpp

```diff
 144 |+     {
 145 |+         auto y = spline.interpolate_at(i*step + x0);
 146 |+         BOOST_CHECK_CLOSE(y, v[i], 10000*std::numeric_limits<Real>::epsilon());
```

> Username: jzmaddock  
> Created_at: 2017-03-02 10:53:49 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103897401  

We can also test in-between the control points since we know what the value is there?

> Username: NAThompson  
> Created_at: 2017-03-02 18:04:27 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103989770  

Fixed.


---

## Review 10 [Commented]

> Username: jzmaddock  
> Created_at: 2017-03-02 10:54:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/57#pullrequestreview-24701686  

📁 test/test_cubic_b_spline.cpp

```diff
 186 |+     {
 187 |+         auto y = spline.interpolate_at(i*step + x0);
 188 |+         BOOST_CHECK_CLOSE(y, v[i], 100000000*std::numeric_limits<Real>::epsilon());
```

> Username: jzmaddock  
> Created_at: 2017-03-02 10:54:05 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103897446  

We can also test in-between the control points since we know what the value is there?

> Username: NAThompson  
> Created_at: 2017-03-02 18:04:34 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r103989795  

Fixed.


---

## Comment 11

> Username: jzmaddock  
> Created_at: 2017-03-02 11:09:22 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-283624942  

Final comment for now... I tried interpolating sin(x) as a test case:  
  
`template<class Real>  
void test_trig_function()  
{  
    std::cout << "Testing that sine functions are interpolated correctly by cubic b splines on type " << boost::typeindex::type_id<Real>().pretty_name() << "\n";  
    std::mt19937 gen;  
    std::vector<Real> v(500);  
    Real x0 = 1;  
    Real step = 0.125;  
  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        v[i] = sin(x0 + step * i);  
    }  
  
    boost::math::cubic_b_spline<Real> spline(v.data(), v.size(), x0, step);  
  
    boost::random::uniform_real_distribution<Real> absissa(x0, x0 + 499 * step);  
  
    for (size_t i = 0; i < v.size(); ++i)  
    {  
        auto x = absissa(gen);  
        auto y = spline.interpolate_at(x);  
        auto yy = sin(x);  
        BOOST_CHECK_CLOSE(y, yy, 100000000*std::numeric_limits<Real>::epsilon());  
        //auto y_prime = spline.interpolate_derivative(i*step + x0);  
        //BOOST_CHECK_CLOSE(y_prime, 2*a*(i*step-x0) + b, 2.0);  
    }  
}  
`  
  
Which works OK - except that all of the tests fail :(  
  
The reason is that the tolerance are too tight, and actually I think we may have the tolerance setting backwards: in this situation the expected error does not depend on the epsilon of the type at all, but rather the precision to which the splines approximate the curve.  So it would be better to rewrite the tests as "we expect N digit accuracy" or whatever.  This also removes the absurdity, that the tests for type float, currently expect no correct digits!

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2017-03-02 12:04:11 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-283635905  

> It would be more consistent with other Boost.Math code to use RealType   
> (even if in retrospect Real might be better - RealType is perhaps a   
> tad 'Hungarian' ;-) )  
>  
  
I'm fairly relaxed about that: I'm not sure we were ever that consistent ;)

---

## Comment 13

> Username: pabristow  
> Created_at: 2017-03-02 12:06:52 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-283636446  

Would fitting a sine function be a suitable simple example of use to go into the /example folder?  Novice users find even the most naive example help in getting started.  Or it there a more real-world example using some data from genetics, physics, say?  If you wish, with your guidance, I could work up an example and add a link to it from the docs, but I'll wait until it is in develop, or a branch therefrom.

---

## Review 14 [Commented]

> Username: NAThompson  
> Created_at: 2017-03-02 18:10:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/57#pullrequestreview-24800109  

Guys, this code review was excellent. I can't fathom how much effort you guys put in to make this project successful, but you both really put in the time to understand what I imagine is a somewhat unfamiliar branch of mathematics and caught a number of problems which would've slipped by me. It is really appreciated.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2017-03-02 18:19:08 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-283734938  

> +There are many use cases for interpolating a function at equally spaced points.  
> +One particularly important example is solving ODE's whose coefficients depend on data determined from experiment or numerically.  
> +Since most ODE steppers are adaptive, they must be able to sample the coefficients at arbitrary points; not just at the points we know the values of our function.  
> +  
> +The routine must be provided the following arguments:  
>  
> Docs fixed. Overloading operator() is a big improvement to usability   
> and readability.  
>  
> I didn't implement PIMPL, though I don't object to that. Copying the   
> object is definitely expensive, but is there a use case? Sharing the   
> function pointer to the overloaded () or the |prime| function seems   
> more reasonable.  
>  
  
Thanks Nick!  
  
On a point of pedantry, still suggest changing:  
  
"The routine must be provided the following arguments:" to  
"The spline is constructed from the following arguments:"  
  
and:  
  
"you may provide two additional arguments to the routine" to  
"you may provide two additional arguments to the constructor"  
  
With regard to use cases for copying the object... good question :)  
  
Note that you can't pass a function pointer to the operator() to a   
routine expecting a functor as you need the "this" pointer as well (some   
dialects of C++ used to have a "closure" type which implemented this as   
a language feature, the alternative is to use std::bind), so there is   
some convenience in using the object directly as a functor.  The only   
use case I have off the top of my head would be for finding the root of   
some function which is hard to compute (or we only have experimental   
data for).  Let me turn this around - what do we loose by making the   
type cheap to copy and move?

---

## Comment 16

> Username: NAThompson  
> Created_at: 2017-03-02 18:38:45 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-283740230  

Docs fixed.  
  
There is one thing that is lost with a PIMPL, which is readability. As it stands, I think anyone could go in and read this routine and understand what it is doing under the hood. People get worn out trying to search through layers of indirection.  
  
However, I'm beginning to think you are correct. One thing I'd like to do is make sure that the spline is easy to pass into an integration routine with signature  
  
    template<class Real>  
    Real integrate((Real) f(Real),  Real a, Real b);  
  
It is a very reasonable requirement that we can pass the spline into this function without a huge memcpy. I'll try to make that happen.

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2017-03-02 18:55:35 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-283744774  

On 02/03/2017 18:38, Nick wrote:  
>  
> Docs fixed.  
>  
> There is one thing that is lost with a PIMPL, which is readability. As   
> it stands, I think anyone could go in and read this routine and   
> understand what it is doing under the hood. People get worn out trying   
> to search through layers of indirection.  
>  
Nod.  
>  
> However, I'm beginning to think you are correct. One thing I'd like to   
> do is make sure that the spline is easy to pass into an integration   
> routine with signature  
>  
> |template<class Real> Real integrate((Real) f(Real), Real a, Real b); |  
>  
> It is a very reasonable requirement that we can pass the spline into   
> this function without a huge memcpy. I'll try to make that happen.  
>  
  
Making it movable would certainly help with some use cases, but not all?  
  
Fleshing out my root-finding use case: imagine you have once per decade   
(census) population data for a city, the question "in what year did the   
population reach 1 million?" is easily answered by interpolating and   
then passing the functor to one of our root-finding algorithms.  You   
could equally replace population with trades on an exchange, cases of   
ebola or whatever the topic of the day was...

---

## Comment 18

> Username: NAThompson  
> Created_at: 2017-03-02 22:30:44 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-283803816  

Ok, just did some testing here to see what happens when we attempt to pass the member function as a functor to (say) `boost::math::tools::bisect`:  
  
    std::vector<double> v(10000000, 0);  
    boost::math::cubic_b_spline<double> b(v.data(), v.size(), 0, 1.0);  
    auto h = std::bind(&boost::math::cubic_b_spline<double>::operator(), &b, std::placeholders::_1);  
    boost::math::tools::eps_tolerance<double> tol;  
    auto p = boost::math::tools::bisect(h, (double) 0, (double) 10, tol);  
  
I ran the call to `bisect` a few million times using `google/benchmark`, the read the disassembly and timing with `perf`. There are no calls to `memcpy`, or `memmove` in the assembly. There is a single `memalloc` and a single `memset`, then quite a bit of horrible stuff to generate the function pointer (but, no huge copy, just a few register->stack and back).  
  
It wasn't even on my radar that someone might just pass the `cubic_b_spline` object directly to `bisect`. If you do that, then yes all the time is spent in calls to `__memmove_sse3_back`. But do people actually think like this? If so then it must be PIMPL'd.

---

## Comment 19

> Username: NAThompson  
> Created_at: 2017-03-02 23:18:45 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-283814477  

Ok, just tested it: No memcpy now using a pimpl when passing as a functor to `bisect`. Benchmarks at 21ns per call, which is identical to before the pimpl.  
  
The only thing I recall is that `std::make_shared` isn't supported by some compilers, so presumably we could change to `boost::make_shared` if necessary.

---

## Comment 20

> Username: NAThompson  
> Created_at: 2017-03-03 15:01:35 UTC  
> Updated_at: 2017-03-03 15:02:14 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-283976326  

It was very easy to add the population example; the `bisect` API is very flexible.  
  
BTW, how do I build the documentation? I'm just trying to osmotically absorb the syntax and hope for the best. I searched around the the documentation on the documentation, but didn't find anything . . .

---

## Review 21 [Commented]

> Username: jeremy-murphy  
> Created_at: 2017-03-05 13:17:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/57#pullrequestreview-25145830  

📁 include/boost/math/interpolators/cubic_b_spline.hpp

```diff
   6 |+ 
   7 |+ // This implements the compactly supported cubic b spline algorithm described in
   8 |+ // "Numerical Analsis, Graduate Texts in Mathematics 181", by Rainer Kress, section 8.3.
```

> Username: jeremy-murphy  
> Created_at: 2017-03-05 13:17:39 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r104312401  

I'm going to be very pedantic and say this reference should be complete, i.e., include the year, publisher, edition, etc and ideally be in one of the usual standard citation formats.  
  
I think these interpolation functions are a great addition to Boost.Math, btw, thanks!

> Username: pabristow  
> Created_at: 2017-03-05 16:50:36 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r104317439  

Pedantry is alive and well at Boost.Math ;-).  I'll make sure that the docs will receive some editorial work in due course to fill out full  references, and links to sources on the web where available, and links to examples, example code etc.  So I hope that John will get this on the develop branch, or a develop/interpolation branch as asoon as convenient.

> Username: NAThompson  
> Created_at: 2017-03-05 16:51:06 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r104317450  

Fixed using MLA format.


---

## Review 22 [Commented]

> Username: jeremy-murphy  
> Created_at: 2017-03-06 12:29:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/57#pullrequestreview-25237442  

📁 include/boost/math/interpolators/cubic_b_spline.hpp

```diff
  43 |+ private:
  44 |+     std::shared_ptr<detail::cubic_b_spline_imp<Real>> m_imp;
  45 |+ };
```

> Username: jeremy-murphy  
> Created_at: 2017-03-06 12:29:32 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r104402436  

Is there a particular reason for using `std::shared_ptr`? I'm not sure that Boost.Math embraces C++11 yet (?) and [Sutter explicitly says](https://herbsutter.com/gotw/_100/) that `unique_ptr` expresses the correct semantics for the PIMPL idiom.

> Username: NAThompson  
> Created_at: 2017-03-06 17:20:20 UTC  
> Updated_at: 2017-05-02 16:33:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#discussion_r104470201  

I tried converting it to a `std::unique_ptr`, but all the syntactic sugar we use in the examples then either must generate an expensive move or doesn't compile at all. In Scott Meyer's "Effective Modern C++",  (end of ch4), using the `shared_ptr` in the PIMPL is discussed as a viable option to `unique_ptr`, but not in detail. I think the fact that we treat the object as a functor really is the killer for the `unique_ptr`. As to using `std::shared_ptr`, I'm fine with using `boost::shared_ptr` if we need to support older toolchains.


---

## Comment 23

> Username: jzmaddock  
> Created_at: 2017-03-06 18:12:54 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-284481788  

> Is there a particular reason for using |std::shared_ptr|? I'm not sure   
> that Boost.Math embraces C++11 yet (?) and Sutter explicitly says   
> <https://herbsutter.com/gotw/_100/> that |unique_ptr| expresses the   
> correct semantics for the PIMPL idiom.  
>  
  
You need a shared_ptr for a pimpl with cheap/shallow O(1) copy.   
unique_ptr expresses something a little different - it has a cheap move,   
but requires a deep-copy which is not what we want here (ie might as   
well not bother with a pimple at all if we're forced to deep-copy).

---

## Comment 24

> Username: jeremy-murphy  
> Created_at: 2017-03-07 00:02:11 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-284575717  

OK, thanks for clarifying the shared vs unique pointer matter.

---

## Comment 25

> Username: NAThompson  
> Created_at: 2017-03-11 21:58:21 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-285903289  

Got the documentation built, but unfortunately the XML that got generated wasn't styled at all and firefox just showed the document tree, so I still can't see how it actually looks. Is there an easy fix for this?

---

## Comment 26

> Username: pabristow  
> Created_at: 2017-03-12 17:47:07 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-285961032  

I'll create a new branch math/develop/splining and build your docs Monday and copy the results to my Dropbox and provide a link for you to see the results.  (I'll correct any obvious snafus too).

---

## Comment 27

> Username: pabristow  
> Created_at: 2017-03-13 17:54:48 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-286189642  

I've merged from Nick's to a new branch off develop splining  (I might rename), after some minor changes, including to math.qbk, putting interpolators into tool part.  
  
https://dl.dropboxusercontent.com/u/43940943/modular-boost/libs/math/doc/html/index.html  
should allow you see the docs as the user will.  Look good to me, but some editorial work would be good (for example I'd like to use code snippets to ensure that the working example code is WYSIWW.  (What You See Is What Works ;-).    __auto should be double I suspect? but using a code snippet will sort this out and protect against unsync of docs and code.  
  
I also think that the folder interpolators (and quadraturers) should be inside the tools folder, but I haven't moved yet.  John - agree?   
And we need your name 'in lights' on the first page.  And some links to test and examples etc. I'll handle this tiresome trivia.  I believe that it is very helpful to the readers.  
  
I've pushed to the new branch splining.  I suggest that John gives you write access to this branch.  When we are all happy, John will merge to develop (and then finally to master for the actual release).  
  
Looking good.

---

## Comment 28

> Username: NAThompson  
> Created_at: 2017-03-13 22:27:11 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-286263371  

>And we need your name 'in lights' on the first page.  
  
I have a commit fixing this and the overuse of `auto` in the docs, ready to be pushed once I have permissions.  
  
Yet again, thanks so much for the excellent code review and holding my hand on all this; it's been a lot of fun.

---

## Comment 29

> Username: pabristow  
> Created_at: 2017-03-14 10:03:44 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-286375310  

Don't worry about either of these items, I will fix __auto using code shippets and adding the author is slightly complex - needs to go in the 'who dun wot' section too.    
  
I suggest a new branch for your quadrature stuff.

---

## Comment 30

> Username: NAThompson  
> Created_at: 2017-05-02 18:29:16 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-298720798  

Ok, I think this guy is ready to go.

---

## Comment 31

> Username: jzmaddock  
> Created_at: 2017-05-02 18:53:46 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-298727350  

Just checking this through.... question: is there any reason to prefer  
  
cubic_b_spline(const Real* const f, size_t length  
  
Over  
  
template <class Iterator>  
cubic_b_spline(Iterator start_range, Iterator end_range  
  
The latter allowing construction from a greater range of stored data types?

---

## Comment 32

> Username: NAThompson  
> Created_at: 2017-05-02 21:15:32 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-298763608  

That's a personal preference of mine, resulting from agonizing pain wrapping C++ into other languages. Raw pointers are way easier to wrap than iterators. But this is not a big priority for me and if you guys want it changed, that's an easy diff to push.

---

## Comment 33

> Username: pabristow  
> Created_at: 2017-05-03 08:23:58 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-298850265  

As an ill-informed interateaphobe, can I just ask is there a reason for not providing *both* forms?  
  
(apart from work involved in writing and testing of course).

---

## Comment 34

> Username: jzmaddock  
> Created_at: 2017-05-03 11:03:11 UTC  
> Url: https://github.com/boostorg/math/pull/57#issuecomment-298881163  

On 03/05/2017 09:23, Paul A. Bristow wrote:  
>  
> As an ill-informed interateaphobe, can I just ask is there a reason   
> for not providing /both/ forms?  
>  
  
That's what I thought too, I have this on a local branch on my machine,   
and the changes look trivial, so I'll sort that out later.  
  
BTW, just for the record, the reason I suggested an iterator based   
interface (apart from being "more C++"), is that many STL   
implementations have debugging iterators which will trap out-of-bounds   
access etc, so it is genuinely safer that way if the data is in an STL   
container to begin with.  But I can see the interoperability argument too...  
  
  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---
