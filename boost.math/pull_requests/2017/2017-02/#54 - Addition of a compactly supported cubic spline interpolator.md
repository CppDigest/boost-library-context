# #54 Addition of a compactly supported cubic spline interpolator. [Closed]

> Username: NAThompson  
> Created at: 2017-02-19 23:22:50 UTC  
> Updated at: 2017-02-24 09:04:30 UTC  
> Closed at: 2017-02-23 23:59:07 UTC  
> Url: https://github.com/boostorg/math/pull/54  

Given a function f, known at evenly spaced samples y_j = f(a + jh),  
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
> Created_at: 2017-02-19 23:36:01 UTC  
> Url: https://github.com/boostorg/math/pull/54#issuecomment-280959427  

This is a "discussion commit". I have had many projects where I need interpolators, extrapolators, numerical intergrators (Romberg, Monte-Carlo, Gaussian), and various other numerical tools which are given primarily in closed source libraries. There seems to be no authoritative open-source numerical library akin to MKL, and as such I was wondering there is support for creating one in boost, and if so what is the proper way to do it.  
  
This commit is a sample of what I would like to do in greater generality under the aegis of boost. However there are still many problems with it, the most notable being the incompatibility with `boost::multiprecision`, lack of unit tests, and no documentation. These will be added if the discussion indicates that there is demand for addition of numerical utilities of this type.

---

## Comment 2

> Username: pabristow  
> Created_at: 2017-02-21 12:29:10 UTC  
> Url: https://github.com/boostorg/math/pull/54#issuecomment-281331595  

This looks useful (as would many of the other things you mention) but the lack of tests and examples, and documentation (and some detailed ways into which it does not quite fit into the Boost.Math framework, but which can probably be fixed) are barriers to acceptance at present.  I can help with documentation, but some tests and example(s) would need your input.  
  
Tools are much more useful if they can be used with Boost.Multiprecision.  What are the barriers to making this possible?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-02-21 12:42:40 UTC  
> Url: https://github.com/boostorg/math/pull/54#issuecomment-281334147  

Apologies on being slow to comment on this...  
>  
> This looks useful (as would many of the other things you mention) but   
> the lack of tests and examples, and documentation (and some detailed   
> ways into which it does not quite fit into the Boost.Math framework,   
> but which can probably be fixed) are barriers to acceptance at   
> present. I can help with documentation, but some tests and example(s)   
> would need your input.  
>  
  
+1 on examples, even a simple one (or two) would help us understand   
where you're going with this and how it should be used.  
  
> Tools are much more useful if they can be used with   
> Boost.Multiprecision. What are the barriers to making this possible?  
>  
  
I haven't looked at the code, but I suspect expression template trip   
things up - it's usually trivial to fix things up for this, it's just a   
case of instantiating with our concept-archetypes and fixing the   
errors.  Get the right interface and make it work well with double and   
we can probably fix up the rest.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2017-02-21 16:00:17 UTC  
> Url: https://github.com/boostorg/math/pull/54#issuecomment-281387121  

Ok, cool. I'll write examples, tests and documentation, and resubmit. You are correct that `boost/math` is not quite the right place for this. Do you think `boost/numeric/interpolators` is better?  
  
Actually getting it to work with boost::multiprecision should be quite easy; I'm using `std::max`, `std::floor` and so on which are only defined on `float`, `double`, and `long double`. . .

---

## Comment 5

> Username: pabristow  
> Created_at: 2017-02-21 17:24:16 UTC  
> Url: https://github.com/boostorg/math/pull/54#issuecomment-281414039  

http://www.boost.org/doc/libs/1_63_0/libs/multiprecision/doc/html/boost_multiprecision/tut/limits/functions.html describes  
  
providing max_value, min_value etc that may be what you need to make your code multiprecision friendly?

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2017-02-21 19:50:02 UTC  
> Url: https://github.com/boostorg/math/pull/54#issuecomment-281459514  

> Actually getting it to work with boost::multiprecision should be quite easy;> I'm using std::max, std::floor and so on which are only defined> on float, double, and long double.  
There is a method for using std functions in Boost.Math in combinationwith built-ins and Multiprecision.  
Consider the scope of a function that uses, let's say, std::floor.Here you can use the using deirective.  
  
For example,  
#include <cmath>  
  
template<typename T>  
T my_function(T x){  using std::floor;  
  x = floor(x);  
  return x;}  
This works for built-ins as well as Boost.Multiprecision types.The Multiprecision function gets found via ADL. The built-infunctions are detected from <cmath>.  
  
Best regards, Chris  
   
  
    On Tuesday, February 21, 2017 5:00 PM, Nick <notifications@github.com> wrote:  
   
  
 Ok, cool. I'll write examples, tests and documentation, and resubmit. You are correct that boost/math is not quite the right place for this. Do you think boost/numeric/interpolators is better?Actually getting it to work with boost::multiprecision should be quite easy; I'm using std::max, std::floor and so on which are only defined on float, double, and long double. . .—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2017-02-22 15:43:48 UTC  
> Url: https://github.com/boostorg/math/pull/54#issuecomment-281706975  

By the way, what is the policy on using MathJax in the documentation? Can I bring in MathJaX.js from their CDN? It looks like the current documents create an SVG from formulas.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2017-02-22 18:32:26 UTC  
> Url: https://github.com/boostorg/math/pull/54#issuecomment-281758915  

On 22/02/2017 15:43, Nick wrote:  
>  
> By the way, what is the policy on using MathJax in the documentation?   
> Can I bring in MathJaX.js from their CDN? It looks like the current   
> documents create an SVG from formulas.  
>  
  
MathJax looks very nice, unfortunately last time I looked into this it   
was pretty much useless for offline docs (ie files on your HD) as most   
(all?) current browsers simply block the scripts :(  
  
We're currently using Mathcast to author MathML (haven't recently   
checked Open Office to see if that works as well), then using the   
SVGMath scripts to convert to SVG.  
  
HTH, John.

---

## Comment 9

> Username: pabristow  
> Created_at: 2017-02-23 09:50:16 UTC  
> Url: https://github.com/boostorg/math/pull/54#issuecomment-281945933  

We've never been happy with generating equations.  We would like both scalable SVG (because they are nice on all resolutions - and widely copied and used elsewhere!) and PNG versions because the using RenderX to render the PDF version is bad using PNG or JPG images.  (PDF version is useful as is standalone, printable and searchable throughout).  If you can get away with one line equations then using inline Unicode symbols in Quikcbook is easiest - see html_symbols.qbk fordefinitions.  LaTeX would be best of all, but we've failed to get it work with the Quickbook toolchain :-(    I can produce these if you send the equation in some form.

---

## Comment 10

> Username: NAThompson  
> Created_at: 2017-02-23 23:59:07 UTC  
> Url: https://github.com/boostorg/math/pull/54#issuecomment-282161402  

Guys, you've all been incredibly helpful here. I'm going to close this PR and resubmit with your advice taken into account.

---

## Comment 11

> Username: pabristow  
> Created_at: 2017-02-24 09:04:29 UTC  
> Url: https://github.com/boostorg/math/pull/54#issuecomment-282240627  

Sounds good.  
  
I think the 'right' place for this in the booth.math tree is alongside the root-solving tools in  
  
modular-boost\libs\math\include\boost\math\tools  
  
(but perhaps John has other more important views).  
  
I'll try to get to look at your work this weekend (since it is going to rain a lot).  
  
(Have you tried switching expression templates off ?  
(They seem to cause trouble sometimes.)  
John and Chris Kormanyos are the experts on multiprecision.  
  
I think we need a test that always passes for routine testing (but you are right in observing that random testing is also informative).

---
