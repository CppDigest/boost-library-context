# #341 ULP plots [Merged]

> Username: NAThompson  
> Created at: 2020-04-09 10:00:04 UTC  
> Updated at: 2020-06-03 22:00:56 UTC  
> Merged at: 2020-04-25 13:03:50 UTC  
> Closed at: 2020-04-25 13:03:50 UTC  
> Url: https://github.com/boostorg/math/pull/341  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-04-11 18:59:32 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-612490895  

I'm going to experiment with this shortly - quick question, what's the use case for multiple calls to add_fn() ?

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-04-11 19:45:15 UTC  
> Updated_at: 2020-04-11 19:50:56 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-612499817  

@jzmaddock: Comparing two different implementations of a function. Right now I'm experimenting with whether transforming the Hermite splines into the Chebyshev basis is more accurate for the Daubechies wavelets.  
  
Here's an example:  
  
https://scicomp.stackexchange.com/questions/34741/reinchs-modification-to-the-clenshaw-recurrence-gives-no-improvement

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2020-04-11 20:56:32 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-612512029  

>Comparing two different implementations of a function.  
  
Doh!  That's a bleddy good use case ;)

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-04-12 08:12:32 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-612580122  

A couple of minor stylistic comments:  
  
* It's nor normally necessary to prefix functions with "get_" or "set_" since they're perfectly overloadable without the prefixes.  So personally I would drop the "set_" prefixes.  
* Can the setting return a reference to the object so that calls can be chained, ie:  
  
```  
ulps_plot& title(std::string const & title);  
```  
  
* I wonder if a "make_ulps_plot" free function would be helpful - it could deduce the template params from it's arguments, and combined with chaining simple cases can be handled with a single line of code:  
  
```  
make_ulps_plot(my_fn, -1.0, 1.0).clip(10).write(myfile.svg");  
```

---

## Comment 5

> Username: NAThompson  
> Created_at: 2020-04-12 12:42:20 UTC  
> Updated_at: 2020-04-12 12:46:54 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-612608471  

> It's nor normally necessary to prefix functions with "get_" or "set_" since they're perfectly overloadable without the prefixes. So personally I would drop the "set_" prefixes.  
  
Yup, fixed. This is much better.  
  
> Can the setting return a reference to the object so that calls can be chained  
  
Yup, fixed. Again a massive improvement.  
  
>  I wonder if a "make_ulps_plot" free function would be helpful - it could deduce the template params from it's arguments, and combined with chaining simple cases can be handled with a single line of code:  
  
I personally could never get the template arguments to be deduced. And  it's the `PreciseReal` type which is hard. The constructor doesn't take a `PreciseReal` parameter. (I learned from the Lambert-W function that the bounds must be `CoarseReal` or you sample outside of the domain.)  
  
I'm not saying it's impossible, I just don't know how to do it!  
  
BTW I set the `no_promote_policy` in the example and the error rates went above the ulp envelope at around x = 2. Not a huge issue, but perhaps an opportunity for improvements. In any case, might need a new example as argument promotion should get every bit correct (it does), and then ulp envelope + argument promotion doesn't make much sense.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2020-04-12 14:55:16 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-612628784  

You can do the make function like this:  
  
```  
template <class F, class CoarseReal>  
auto make_ulps_plot(F f, CoarseReal a, CoarseReal b, size_t samples = 10000, bool perturb_abscissas = true, int random_seed = -1)  
{  
   static_assert(std::numeric_limits<CoarseReal>::min_exponent != std::numeric_limits<CoarseReal>::max_exponent, "The abscissa values must be floating point types - not integers!!");  
   return ulps_plot<F, decltype(f(a)), CoarseReal>(f, a, b, samples, perturb_abscissas, random_seed);  
}  
```  
  
which seems to work for me....

---

## Comment 7

> Username: NAThompson  
> Created_at: 2020-04-12 15:18:02 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-612631981  

Doesn't this assume that when `f` is called with a `CoarseReal` then the return type is a `PreciseReal`? I'm not sure this holds in general; for example a lambda with an `auto` argument-- see [here](https://stackoverflow.com/a/54188679/904050).

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2020-04-12 18:02:59 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-612653992  

Ah, you got me there, it doesn't work with generic lambdas or functors :(  
  
You could add an assert that the deduced return type has greater precision than the type being tested, and then document the limitation I guess?

---

## Comment 9

> Username: NAThompson  
> Created_at: 2020-04-12 19:20:35 UTC  
> Updated_at: 2020-04-12 19:20:53 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-612663507  

> You could add an assert that the deduced return type has greater precision than the type being tested, and then document the limitation I guess?  
  
I'm a bit torn here, since your suggestion is a clear win for getting the API more ergonomic. But imagine that you wanted to test a software implementation against a hardware implementation known to deliver half-ulp accuracy, at the same precision. Then the strictly greater precision requirement is unreasonable. Also, you don't just want > precision, you might actually need quad precision to test a double precision implementation, rather than (say) long double.  
  
What we really want here is something akin to the `argument_type` and `result_type` of [std::function](https://en.cppreference.com/w/cpp/utility/functional/function), but for some reason it got deprecated in C++17. Does anyone know the story?

---

## Comment 10

> Username: pabristow  
> Created_at: 2020-04-14 11:00:00 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-613372928  

I:\boost\libs\math>git log  
commit 05b664b30d098c1efd2cd96f065a7587d2c5ff4e (HEAD -> ulp_plot, origin/ulp_plot)  
Author: Nick <nathompson7@protonmail.com>  
Date:   Sun Apr 12 12:53:27 2020 -0400  
    Fix bug when < 0.5 ulp accuracy occurs everywhere. clip can be nonintegral. [CI SKIP]  
  
MSVC 16.6.0 Preview 2.1 c++std = C++17   
  
(mutters about  
I:\boost\boost\math\tools\ulps_plot.hpp(150,20): warning C4244: 'initializing': conversion from 'double' to 'int', possible loss of data - but not relevant to results  
)  
  
[airy_ai_float.7z.zip](https://github.com/boostorg/math/files/4474860/airy_ai_float.7z.zip)  
  
shows what I get with no changes to the current I:\boost\libs\math\example\airy_ulps_plot.cpp  
  
    using PreciseReal = long double;  
    using CoarseReal = float;  
  
Is this what I should expect?  (that is, is that what you get?)  
  
Will now try using cpp_bin_float_quad for precise.

---

## Comment 11

> Username: NAThompson  
> Created_at: 2020-04-14 11:08:20 UTC  
> Updated_at: 2020-04-14 11:19:34 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-613376982  

@pabristow : That's what I got as well. You're clip is set to 2.5 ulps, which is still very tight. Try setting the clip = 20 to see some structure.  
  
(Also, I removed the warning . . . )

---

## Comment 12

> Username: pabristow  
> Created_at: 2020-04-14 11:32:17 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-613387835  

After enough time for a quick coffee,   
  
    using PreciseReal = cpp_bin_float_quad;  
    using CoarseReal = float;  
  
[airy_ai_float_quad.7z.zip](https://github.com/boostorg/math/files/4474969/airy_ai_float_quad.7z.zip)  
  
which doesn't look very different?  
  
What am I doing wrong or mis-expecting?

---

## Comment 13

> Username: NAThompson  
> Created_at: 2020-04-14 11:38:50 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-613390713  

It's correct; the clip is just very tight. Try  
  
```  
plot.clip(20);  
```

---

## Comment 14

> Username: NAThompson  
> Created_at: 2020-04-14 11:41:14 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-613391700  

Note that a sample outside the ULP envelope is not an indication that the function is incorrect. The ULP envelope gives the best accuracy that can be expected under the assumption that the only source of error is rounding to the nearest representable abscissa.

---

## Comment 15

> Username: pabristow  
> Created_at: 2020-04-14 13:36:33 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-613447122  

Ah - yes - I see roughly what I expect now :-)  Intuitively, the envelope feels x2 too tight?  Might it contain something like 95% of points?  
At abscissa around 2 there are bigger numbers outside the envelope, and then it gets better.  I have yet to think what that might mean. I'll play some more. Thanks.

---

## Comment 16

> Username: pabristow  
> Created_at: 2020-04-14 14:37:11 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-613480925  

I have also tried a smaller number of points, even as few as 100 gives one the flavor of the 'fit' (though 200 is better.  This reduces the compute time, even with cpp_bin_float_cpp, to a few sips of coffee rather than a whole cupful, including cooling time!  It also reduces the file size down a lot, a tiny 20K for 200 points and only for 500 points.  This plot looks so helpful to me that it might be good to have it small enough that we can afford provide it for many or all functions?  (We are partly responsible for the 30 Tbyte of Boost downloads (eyes passim - as they say in Private Eye) so we need to take some heed of size).  Fortunately SVG is highly compressible in zips but it only partly compensates for the inefficient way all the SVG plots repeat the diameter and color and etc for each of hundreds of points.  
   
I looked at the white background version and found it needs a bigger marker 1.5 px looked good.  Black looked better to me that sleelblue. Curious how the effect is so different between negative and positive.  
  
Perhaps we could have a second wider x2 envelope in paler green?  
  
I've been exploring finite_difference_differentiation with Lambert_w (where we have analytical version as the reference 'known-good' prime).  I'll look at ulps_plot applying to Lambert_w.  
  
V cool!

---

## Comment 17

> Username: NAThompson  
> Created_at: 2020-04-14 14:48:26 UTC  
> Updated_at: 2020-04-14 14:51:37 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-613487400  

>  Intuitively, the envelope feels x2 too tight?  
  
Interesting that you caught this. Yes, I agonized about this for some time. If then envelope assume *faithful rounding*, i.e., we only know that x lies between two representables, rather than is rounded to the closest representable, then the envelope should be twice as large. But eventually I decided that I wanted the envelope to represent the best you can possibly do, as there are a really large number of assumptions you could make to widen the envelope, but no assumptions that could narrow it.  
  
> At abscissa around 2 there are bigger numbers outside the envelope, and then it gets better. I have yet to think what that might mean.  
  
I think there are opportunities for improvement there!  
  
> Perhaps we could have a second wider x2 envelope in paler green?  
  
I think that's reasonable; let me see if how that looks.  
  
> I've been exploring finite_difference_differentiation with Lambert_w (where we have analytical version as the reference 'known-good' prime).  
  
Keep in mind that even theoretically, finite differences don't control relative error! The ULP plot is all about how well you're controlled your relative error and how efficiently you've used your floating point type.

---

## Comment 18

> Username: pabristow  
> Created_at: 2020-04-14 16:11:36 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-613535517  

It doesn't make things **look** good :-(    
  
Needs careful and sympathetic documentation.   
So what does the difference between points inside the envelope mean?  
Unachievable reliably without casting from a higher precision type?  
  
I still haven't got my head all the way round this :-(  Tomorrow perhaps...  
  
Aside should airy_ulps_plot be airy_ulp_plot (singular - as the branch name?)  
  
sqrt_ulp_plot, cbrt_ulp_plot, lambert_wo_ulp_plot...  
  
ULP is a feature/trait of the floating-point representation type?  
  
Or should they perhaps be called condition plots?  Just pondering before we get too far in to change.

---

## Comment 19

> Username: NAThompson  
> Created_at: 2020-04-14 16:53:59 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-613557989  

> So what does the difference between points inside the envelope mean?  
  
Inside the envelope, you've done as much as you can do; there is *absolutely* no room for improvement-outside of casting down from a higher precision type-which can always achieve 0.5 ULP accuracy. Outside the envelope, there *might* exist some way to improve the evaluation.  
  
> Aside should airy_ulps_plot be airy_ulp_plot (singular - as the branch name?)  
  
I changed it from singular to plural to match [Cleve Moler's language](https://blogs.mathworks.com/cleve/2017/01/23/ulps-plots-reveal-math-function-accurary/).  
  
> ULP is a feature/trait of the floating-point representation type?  
  
Yes; the distance between one representable and the next greater is 1 ULP. Note how 1 ULP is a different size past each power of 2!

---

## Comment 20

> Username: NAThompson  
> Created_at: 2020-04-14 22:09:21 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-613706236  

I'm now torn on this; maybe a max(1, cond[f](x)) is better than max(0.5, cond[f](x)/2)?  
  
There's a good case to be made for either of them . . .  
  
@jzmaddock : Got an opinion on this one?

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2020-04-15 15:49:27 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-614120845  

>I'm now torn on this; maybe a max(1, condf) is better than max(0.5, condf/2)?  
  
I'm not sure there is a correct answer - I guess a "perfect" implementation would show .5 ulp error so that's the bound we should show?  
  
BTW I'm seeing rather different scatter plots currently to an earlier version of this.... need to investigate some more.

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2020-04-15 16:05:02 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-614129669  

Here we go - before and after the recent round of changes testing std::exp  
  
```  
      boost::math::tools::ulps_plot<pf2_t, boost::multiprecision::cpp_bin_float_50, double> plot(pf2, -10, 10);  
      plot.set_clip(10);  
      plot.add_fn(cf);  
      plot.write("exp.svg");  
```  
  
![old](https://user-images.githubusercontent.com/5011768/79359798-eda3ff00-7f3a-11ea-93ca-2786a9b8e8bb.png)  
![new](https://user-images.githubusercontent.com/5011768/79359803-ef6dc280-7f3a-11ea-9191-6f4346da3e1c.png)  
  
BTW the default color scheme is nice indoors, but I've just found the points invisible sitting outside in the sunshine...

---

## Comment 23

> Username: NAThompson  
> Created_at: 2020-04-15 16:08:05 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-614131310  

> BTW I'm seeing rather different scatter plots currently to an earlier version of this.... need to investigate some more.  
  
I changed the default from `perturb_abscissas=true` to `perturb_abscissas = false`; is that it?

---

## Comment 24

> Username: pabristow  
> Created_at: 2020-04-15 16:08:53 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-614131774  

I agree that we should show the "perfect" envelope (that you can only ever get by down-casting from a higher precision), but that isn't a reasonable realistic expectation, that you can get some common methods like polynomial fits, which might be about twice as much.  
  
Why can't we show *both*?  
  
PS All right for some in the sunshine (a rarity in UK but nationwide at present - Yay!).  I found that a bigger diameter 1.5 was clearer in white.

---

## Comment 25

> Username: NAThompson  
> Created_at: 2020-04-15 16:10:14 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-614132513  

> Why can't we show both?  
  
I think that's reasonable; lemme see how it implements.

---

## Comment 26

> Username: jzmaddock  
> Created_at: 2020-04-15 16:54:31 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-614156334  

>I changed the default from perturb_abscissas=true to perturb_abscissas = false; is that it?  
  
I missed that, yes that's it, thanks.  
  
Question: should perturb_abscissa be a member function rather than set once in the constructor?  It would allow you to compare the same function with exact and inexact inputs for example.

---

## Comment 27

> Username: NAThompson  
> Created_at: 2020-04-15 20:45:11 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-614270763  

> Question: should perturb_abscissa be a member function rather than set once in the constructor?   
  
Well, that's a bit hard, since the way it's currently set up, you query the accurate function in the constructor and then never use it again. If you were going to make it a member function, you'd have to store the function and keep track of whether the abscissas are perturbed or not.  
  
I'm wondering how meaningful the `perturb_abscissas` option really is. I think the condition number envelope gives you the same information . . .

---

## Comment 28

> Username: pabristow  
> Created_at: 2020-04-16 09:03:14 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-614516232  

I'm relieved to hear that you are considering this, as I've be puzzling what it is for too.  It's confusing (and I suspect a bit wrong?)  
  
And while I am ranting, with all due respect to Cleve Moler, a man with a neuron count at least an order of magnitude greater than mine, ulps_plot is a **terrible** name.   
  
(We should be labelling the axes properly).  
  
Having names like eps, epsilon and ulp that are only nitpickingly different is yet another wayto befuddle the users who never wanted  to know about them in the first place!  
  
Nomenclature is a massive barrier to understanding.

---

## Comment 29

> Username: NAThompson  
> Created_at: 2020-04-16 09:14:13 UTC  
> Updated_at: 2020-04-16 09:27:21 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-614522237  

> I'm relieved to hear that you are considering this, as I've be puzzling what it is for too. It's confusing (and I suspect a bit wrong?)  
  
Well, it does help answer the question "how meaningful is it when I've gotten half-ulp accuracy?" If rounding to the nearest abscissa causes you to lose 20 ulps, and it costs 4x as much to achieve half-ulp as it does to achieve 1ulp, does that tradeoff make sense? But it might be optionitis.  
  
> And while I am ranting, with all due respect to Cleve Moler, a man with a neuron count at least an order of magnitude greater than mine, ulps_plot is a terrible name.  
  
Well, the unit on the y-axis is ulps. Generally we do pluralize the units, say "seconds vs kilometers", rather than "second vs kilometer".  
  
> Having names like eps, epsilon and ulp that are only nitpickingly different is yet another way to befuddle the users who never wanted to know about them in the first place!  
  
ulps and epsilon are different. An ulp = epsilon only on the interval [1,2]. On the interval [2,4], an ulp = 2*epsilon. As you get close to zero or go out to infinity, they become dramatically different.

---

## Comment 30

> Username: pabristow  
> Created_at: 2020-04-16 11:19:12 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-614589280  

"If rounding to the nearest abscissa causes you to lose 20 ulps, and it costs 4x as much to achieve half-ulp as it does to achieve 1ulp, does that tradeoff make sense?"  
  
That's starting to be more useful to your audience (who are mostly bitty rather than mathy).  
  
Are there two things here?  
1 How much effect does the number of bits in the floating-point representation have?  
(related to   
1a "If the function is reversed, how many floating-point representations give the initial x?"  
2  How near 'as-good-as-it-gets' is the algorithm?  
  
What users want to know is if they need more bits - long double (or quad) rather than double?  
(Of course, if value x a measurement, getting more bits may be difficult?)  
Or if the algorithm is not yet ideal?  
  
PS I think the y-axis units label should be singular 'ulp' and have a name, perhaps 'condition (ulp)' to conform to normal practice like 'time (s)'.

---

## Comment 31

> Username: NAThompson  
> Created_at: 2020-04-16 11:54:04 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-614605796  

> How much effect does the number of bits in the floating-point representation have?  
  
It should have no effect on the graph, since it is relativized to the unit roundoff.  
  
  
Question 1a and 2 are very strongly related, so I wouldn't say there are two things here; they are basically the same.  
  
> What users want to know is if they need more bits - long double (or quad) rather than double?  
  
An ulps plot won't help them if that's the question they are asking. An ulps plot only answers the question "given a bit budget b, how efficiently am I using it?"

---

## Comment 32

> Username: pabristow  
> Created_at: 2020-04-16 14:01:27 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-614672253  

9f4ebf6823f96b0bd5c105d1d9e96371a07da03c adds a comment  
  
"We recommend using at least `long double` to test `float` precision, and `boost::multiprecision::float128` to test `double` precision values."  
  
float128 is GCC only, boost::multiprecision::cpp_bin_float_quad is portable (but slower).  
  
long double is useless on MSVC, (long double == double) but one can use   
  
boost::multiprecision::cpp_bin_float_cpp_bin_float_double_extended instead. == 80-bit  
  
But I'm surprised that it needs so much more precision, for example more than double the precision for  float to long double???

---

## Comment 33

> Username: NAThompson  
> Created_at: 2020-04-16 14:10:00 UTC  
> Updated_at: 2020-04-16 14:11:54 UTC  
> Url: https://github.com/boostorg/math/pull/341#issuecomment-614677147  

> But I'm surprised that it needs so much more precision, for example more than double the precision for float to long double???  
  
Well, there's no hard requirement to use `long double` to test `float`. But even `std::exp`, which has a very sensible condition number growth requires twice the precision to recover the half ULP value by casting. So it's a good starting point.  
  
The point is you want to ensure your reference value is correct.

---
