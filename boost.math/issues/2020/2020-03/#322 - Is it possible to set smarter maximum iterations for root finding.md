# #322 - Is it possible to set smarter maximum iterations for root finding? [Closed]

> Username: pabristow  
> Created at: 2020-03-10 12:53:34 UTC  
> Updated at: 2024-01-12 18:14:31 UTC  
> Closed at: 2020-09-07 13:34:37 UTC  
> Url: https://github.com/boostorg/math/issues/322  

Reflecting on https://github.com/boostorg/math/issues/316 reminded me that when writing some examples of root finding, I noted the possible ill-effect of relying on the default max_iter setting, and wrote  
  
>The final parameter specifying a maximum number of iterations is optional. However, it defaults to boost::uintmax_t maxit = (std::numeric_limits<boost::uintmax_t>::max)(); which is 18446744073709551615 and is more than anyone would wish to wait for!   
So it may be wise to chose some reasonable estimate of how many iterations may be needed, In this case the function is so well behaved that we can chose a low value of 20.    
  
in `https://www.boost.org/doc/libs/1_72_0/libs/math/doc/html/math_toolkit/root_finding_examples/cbrt_eg.html`  
  
From the rate of convergence reported, I wondered if some heuristic could be devised, based mainly on the number of decimal digits in the type, with some generous margin, say times 2, could be provided in place of the 'wait-for-ever' default?   
  
Obviously some really badly behaved examples may converge with more iterations, but at least users will get an timely error message, hinting more iterations, rather than an apparent 'hang'.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-03-10 12:56:45 UTC  
> Url: https://github.com/boostorg/math/issues/322#issuecomment-597070864  

We already set an upper limit on the number of iterations - that was the cause of the bug report - the upper limit was being hit.  
  
Or did you mean change the default used when users use root finding rather than when we use it?

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-03-10 13:06:42 UTC  
> Url: https://github.com/boostorg/math/issues/322#issuecomment-597075137  

Here's the comment I left on the complex Newton's method:  
  
```cpp  
/*  
   * Why do we set the default maximum number of iterations to the number of digits in the type?  
   * Because for double roots, the number of digits increases linearly with the number of iterations,  
   * so this default should recover full precision even in this somewhat pathological case.  
   * For isolated roots, the problem is so rapidly convergent that this doesn't matter at all.  
   */  
template<class Complex, class F>  
Complex complex_newton(F g, Complex guess, int max_iterations = std::numeric_limits<typename Complex::value_type>::digits)  
{  
```  
  
I stand by this being a reasonable maximum number of iterations!

---

## Comment 3

> Username: pabristow  
> Created at: 2020-03-10 14:20:48 UTC  
> Url: https://github.com/boostorg/math/issues/322#issuecomment-597111995  

@NAThompson `int max_iterations = std::numeric_limits<typename Complex::value_type>::digits  ` does indeed seem a good heuristic - I imagined something a bit like this, perhaps with a bit extra to allow for the less-well-behaved?  
  
@jzmaddock Yes, I am suggesting for the user (and we might use the what the heuristic iterations ourselves too?)  Leaving as max() doesn't seem ideal?  Just wondering...

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-03-10 15:50:15 UTC  
> Url: https://github.com/boostorg/math/issues/322#issuecomment-597161476  

`numeric_limits<>::digits` isn't the worst default ever, but be careful - Newton (and Halley etc) will fall back to bisection in the worst case, and bisection requires `numeric_limits<>::digits` iterations or less only when the endpoints of the range are tight to begin with (as the range halves in size with each iteration).    
  
So a typical worst case would be non-tight bounds, and an initial Newton (or Halley) step that shoots off to one endpoint, followed by lots of bisection to get back to the root.  
  
An example of a function that behaves that way is the incomplete beta with large parameters - the function become something akin to a square wave, and you can be within 10^-6 or so of the root and still be in the section of the curve that's flat-lining, so the first Newton step shoots off to +- infinity.  The variable we're locating varies from 0-1 and if the root is near 0 then it's a long way down to bisect all the way to 10^-300 (or even lower for long double).  In fact there are some cheeky heuristics in place to perturb the next best guess and prevent "eternal bisection" down to zero, but it still takes quite a few iterations.  
  
BTW the current default limit is 200 for all Boost.Math code, which is still executed pretty quickly for most functions, and is also not unreasonable, though still clearly imperfect!

---

## Comment 5

> Username: pabristow  
> Created at: 2020-03-10 17:17:08 UTC  
> Url: https://github.com/boostorg/math/issues/322#issuecomment-597207635  

I'm sure there some gnarly ill-tempered functions about, but I am trying to think about it from a users point of view.  As I understand it, by default, a user will get 18446744073709551615  iterations, an inconveniently long time on any computer ;-)  The computation will appear to hang (and be killed, giving no clue as to how many iterations were tried), or consume resources until some timeout is reached if some CI scheme.  My concern is that this isn't as helpful as I think we could be.  
  
We could suggest 200, but that's over-generous for most applications.  Wouldn't it be better to suggest a more realistic max iter, twice or thrice digits, say.  If it runs out of iterations, the user at least gets some clue what's going wrong.  He can then easily up the max iter count (he also gets told how many is not enough), or he can investigate why it is isn't converging by switching the diagnostics macro on?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2020-03-10 18:20:38 UTC  
> Url: https://github.com/boostorg/math/issues/322#issuecomment-597239237  

Paul, I'm not against changing the user-default if they don't explicitly specify a limit, just warning that changing our internal defaults will likely break some stuff.  
  
Care to submit a PR?  It's a trivial code change, but the docs will need a bit of checking through I'm afraid.

---

## Comment 7

> Username: pabristow  
> Created at: 2020-03-11 09:22:29 UTC  
> Url: https://github.com/boostorg/math/issues/322#issuecomment-597526640  

I'm NOT suggesting changing any of our iteration limits - we've already avoided the iterate for ever trap by setting a limit (if often over-generous - but that's really doesn't matter at all).  
  
I'll prepare a PR to change the *user* default, and the docs too.

---

## Comment 8

> Username: pabristow  
> Created at: 2020-03-14 17:26:49 UTC  
> Url: https://github.com/boostorg/math/issues/322#issuecomment-599104379  

I've looked into my proposal to change the *default* maximum iterations in our root-finding algorithms, now boost::unint max().  
  
TL;DR   
  
We could  
  
1 Recommend numeric_limits digits (times a small factor?) in the documentation  
  
and/or  
  
2  Change code to provide these as defaults.  
  
Details  
  
I'm impressed that it is really quite hard to generate total failure to converge.   
All the contributors and the author worked hard and very effectively on this :-)  
  
But I can confirm that it *is* possible to get into an endless loop:   
you have to kill the .exe (but no clues on when), and you don't get any clues why or what to do.  
   
My main thesis is that this is *really unhelpful* to both Real Humans and Automata.  
  
My proposed changes.  
  
For example:  
  
```halley_iterate(F f, T guess, T min, T max, int digits)```  
  
-   boost::uintmax_t m = (std::numeric_limits<boost::uintmax_t>::max)();  
+   boost::uintmax_t max_iter = std::numeric_limits<T>::digits;  
```  
  
and similarly for newton-raphson and schroeder.  
  
The function  `complex_newton` already uses `int max_iterations = std::numeric_limits<typename Complex::value_type>::digits`,  
so no change here.  
  
For the TOMS749 algorithm, I suggest to provide a new variant with a default for max iterations (and default policies):  
  
```template <class F, class T, class Tol>  
inline std::pair<T, T> bracket_and_solve_root(F f, const T& guess, const T& factor, bool rising, Tol tol)  
{  
- // boost::uintmax_t max_iter = std::numeric_limits< boost::uintmax_t >::max();  May loop 'forever'.  
  
+  // Heuristic: estimate of maximum iterations possibly needed, unless very badly behaved.  
  boost::uintmax_t max_iter = std::numeric_limits<T>::digits * 3;  // times 3 for a big margin for really poor choices of parameters, guess etc.  
  return bracket_and_solve_root(f, guess, factor, rising, tol,  
  max_iter,  
  policies::policy<>()); // Default policies for all errors.  
}  
```  
  
I'm not sure, but I feel that the derivative methods are so much more effective that just `std::numeric_limits<T>::digits` is OK, but for the no-derivative method I found cases that would eventually converge in rather more iterations, so I suggest digits * 3 (for double 53 * 3 -= 159) for this.  (This concurs with the default max_iterations = 200 used for all the Boost.Math tests, which is unchanged of course).  
  
All *our* examples and tests that I can find use the full parameters variant to set the iteration count   
(and to be able to retrieve the count at the end).  
  
So I don't think this has any impact on our testing or examples, and my retests locally confirm this.  
  
Where it *may* make a difference is for users who haven't bothered to set an iteration max,  
and have some seriously badly-behaved functions, or really ill-chosen guesses etc.  
  
I've played with the cbrt (admittedly a well-behaved function) and abused the guess, tolerance and max and min etc.  
  
I've compared a noderiv bracket_and_Solve_root  (TOMS748) with a Newton-Raphson.  
  
With the above changes their job will end with a warning that they are getting through a lot of iterations without meeting the tolerance  
(as in the case that sparked this idea).  
  
I believe that in most cases, this shows things have gone badly wrong anyway, and some changes are needed,  
  
at least perhaps a much better guess, or looser tolerance, etc, or a specific big max iterations like 1000.  
  
Whatever, I think that it is clearly better to specify a reasonable max iterations count.  
  
If you agree, I will firm up changes of the default for the four functions for final review.

---

## Comment 9

> Username: jamadagni  
> Created at: 2024-01-11 10:43:17 UTC  
> Updated at: 2024-01-11 10:44:21 UTC  
> Url: https://github.com/boostorg/math/issues/322#issuecomment-1886833066  

What happened to this? in Mar '20 a change to max_iter was suggested by the same person who closed this in Sep '20 but I don't see any update to the API as suggested.  
  
I am interested in APS (aka TOMS748) and wrote [apsfind](https://github.com/jamadagni/apsfind) based on Boost. So if there is an improvement I would be interested to know.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2024-01-12 18:14:30 UTC  
> Url: https://github.com/boostorg/math/issues/322#issuecomment-1889749077  

>What happened to this? in Mar '20 a change to max_iter was suggested by the same person who closed this in Sep '20 but I don't see any update to the API as suggested.  
  
I'm not sure why Paul closed this (and he's sadly passed away now), but in any case since we always require a specific max number of iterations for TOMS748, this issue doesn't really effect that algorithm - unless we added new signatures with no max iterations parameter.  So far we've had no demand for that convenience signature.
