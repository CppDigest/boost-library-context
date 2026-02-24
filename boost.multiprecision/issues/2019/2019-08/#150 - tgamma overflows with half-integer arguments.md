# #150 - tgamma overflows with half-integer arguments [Open]

> Username: kshegunov  
> Created at: 2019-08-06 14:38:04 UTC  
> Updated at: 2021-01-19 19:59:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/150  

The following MRE illustrates that tgamma overflows (i.e. returns infinity) when half-integers are used:  
```  
    using namespace boost::multiprecision;  
    typedef number<backends::cpp_bin_float<8 * std::numeric_limits<double>::digits, backends::digit_base_2, void, boost::int16_t, -1022, 1023>, et_off>  float8x;  
  
    long double t = 3.5l;  
    float8x tt = 3.5l;  
  
    long double r = tgamma(t);  
    long double rr = (long double) tgamma(tt);  
```  
Produces 3.32335097044784255117 for `r`, but infinity for `rr`. In contrast:  
```  
    float8x tt = 3.l;  
    long double rr = (long double) tgamma(tt);  
```  
Produces the correct value of 2.  
  
(g++ 8.3.0 on Linux, `long double` is 80bit extended)

---

## Comment 1

> Username: kshegunov  
> Created at: 2019-08-06 16:52:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/150#issuecomment-518753354  

I'm now convinced this is a bug in g++, not in the lib code. Sorry for the noise.  
For the record:  
```  
boost::math::tgamma<float8x>(tt);  
```  
Works correctly (if the template argument's provided explicitly). Without much investigation, my best guess is that overload resolution with implicit conversion (no idea why) overrides template instantiation or even more likely that overloading rules aren't strictly followed.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-08-06 17:13:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/150#issuecomment-518761584  

No this is a bug related to the limited exponent range of the number type.  What's happening inside tgamma(z) is:  
  
* We estimate a size for N, such that tgamma(z+n) can be evaluated by asymptotic expansion (Stirling's approximation).  
* Evaluate tgamma(z+N) and recurse down to tgamma(z).  
  
This works fine as long as exponent range and precision increase in step (at least somewhat), but fails catastrophically in this case because we have a high precision (and hence need a largish value for N), but the low exponent range means that tgamma(z+N) always overflows :(  
  
I'm not sure how to fix this at present...

---

## Comment 3

> Username: kshegunov  
> Created at: 2019-08-06 17:23:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/150#issuecomment-518765023  

Indeed, you're right. I've hit it again and went through the stack a bit. It seems I'm triggering the  
```  
if(log_gamma_value > tools::log_max_value<T>())  
```  
check causing the overflow. As a workaround I can increase the exponent size at least for this one (i.e. define a separate type to do the calculation). This code path is neither time nor efficiency critical. Thanks for looking into it and for the information!

---

## Comment 4

> Username: ckormanyos  
> Created at: 2021-01-17 19:22:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/150#issuecomment-761865277  

I am going through the multiprecision issues and find myself interested in this one.  
  
Let's limit this discussion at first to real argument greater than zero.  
  
@jzmaddock: Do you know if this is issue related to the argument's _nearness_ to a half-integer, or it solely related overflow problems regarding small exponent range, regardless of the argument?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-01-17 21:18:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/150#issuecomment-761881657  

Purely exponent size - as the digit count increases so minimum_argument_for_bernoulli_recursion increases - eventually to the point where tgamma at that point is infinite if the exponent range is small.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2021-01-18 11:02:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/150#issuecomment-762173040  

Hi @jzmaddock, I was revisiting integer sequences such as [A001163](https://oeis.org/A001163) and [A001164](https://oeis.org/A001164), which basically list hundreds of coefficients for Stirling's approximation to the Gamma function.  
  
These could provide uniform asymptotic series expansion (though i believe ultimately divergent) for many digits of `tgamma()`. I do have some theoretical work from Gil, Seguar, and Temme (ISBN  978-0898716344, present in my personal library) regarding the generation of unlimited coefficients, but only in algebraic form. I also ran across a simpler uniform expansion of the Gamma function relying, however, on an initialization with a value of the `erfc` function. These are in Sects. 8.3.1 and 8.3.4 of the book mentioned above.  
  
I could look into these possibilities, albeit slowly..., for converging methods in the numerical limits range pertinent for this issue.  
  
What do you think?

---

## Comment 7

> Username: ckormanyos  
> Created at: 2021-01-18 11:22:53 UTC  
> Updated at: 2021-01-18 11:23:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/150#issuecomment-762184791  

Sometimes I extend the coefficients in A&S 6.1.34 for `1/tgamma(z)` to more digits, resulting in coefficients something like those shown below. I use this kind of list on a fraction of the unit disk in the complex plane.  
  
```  
 1.  
 0.57721566490153286060651209008240243104215933593992359879909521718564803164756,  
 -0.65587807152025388107701951514539048127976638047858434729236244567546030507157,  
 -0.04200263503409523552900393487542981871139450040110609352206581290082023317655,  
  0.16653861138229148950170079510210523571778150224717434057046890322230681673101,  
-0.04219773455554433674820830128918739130165268418982248637691887333199768899677,  
-0.00962197152787697356211492167234819897536294225211300210513886262941690775947,  
  0.00721894324666309954239501034044657270990480088023831800109478118077273942752,  
-0.0011651675918590651121139710840183886668093337953840574434075052781817253968,  
-0.00021524167411495097281572996305364780647824192337833875035026723900327010319,  
  0.00012805028238811618615319862632816432339489209969367721490054598241575452853,  
-0.00002013485478078823865568939142102181838229483329797911526116273821671760797,  
-1.25049348214267065734535947383309224232265562115395981534992256836680048.E-6,  
  1.13302723198169588237412962033074494332400483862107565429550540114939979.E-6,  
-2.0563384169776071034501541300205728365125790262933794534683172532736459.E-7,  
  6.11609510448141581786249868285534286727586571971232086732402928838098.E-9,  
  5.00200764446922293005566504805999130304461274249448171895337887995201.E-9,  
-1.18127457048702014458812656543650557773875950493258759096189264505716.E-9,  
  1.043426711691100510491540332312250191400709823125812121087107392735.E-10,  
  7.78226343990507125404993731136077722606808618139293881943550732695.E-12,  
-3.6968056186422057081878158780857662365709634513609951364845465544.E-12,  
  5.1003702874544759790154813228632318027268860697076321173501048566.E-13,  
-2.058326053566506783222429544855237419746091080810147188058196444.E-14,  
5.34812253942301798237001731872793994898971547812068211168095494E-15  
```  
  
But I have not yet found a really good high-precision method for `tgamma()` other than the ones we already use...

---

## Comment 8

> Username: jzmaddock  
> Created at: 2021-01-18 13:43:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/150#issuecomment-762259087  

I don't think we can extend the range of the coefficients - I believe the series become divergent rather promptly unfortunately.  
  
We could compute lgamma to avoid the initial overflow, but then subtracting values would lead to horrible cancellation error and a meaningless result.  
  
Something I did a lot in the hypergeometric functions - though it's a ton of work - is to rescale everything, so for example instead of computing n^n we could compute n^(n/2) and then rescale by that factor once the current result becomes small enough... but dividing by 2 might not be enough and we might have to rescale multiple times :(  
  
I wonder though: is this an issue at all once the exponent range matches that of `long double` ?  Also if we could predict up front when the issue will occur via constexpr arithmetic, we could static_assert with a useful error message?

---

## Comment 9

> Username: ckormanyos  
> Created at: 2021-01-18 14:00:13 UTC  
> Updated at: 2021-01-18 14:01:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/150#issuecomment-762268569  

> I don't think we can extend the range of the coefficients   
  
I was on the wrong track with that research direction. My resources were actually for the _incomplete_ Gamma function for large parameter _a_. I did, however find an accelerated Stirling's approximation for `tgamma()` itself. But I'm not at the moment capable to comprehend how the coefficients could be generated for multiple precision.  
  
> Also if we could predict up front when the issue will occur via constexpr arithmetic, ...  
  
Yes. In these (predicted) cases, we could also concentrate on computing the real-valued Gamma function of argument `x` first scaled with `1 <= x < 2` and then recurse up and up or down by 1. A super-easy approach I sometines take is really trivial. I compute a least-squares multiple precision best fit of a curve of tgamma in the range 1...2. This might end up with a polynomial with, say, 64 coefficients giving 100 decimal digits of precision. The problem is, this is then fixed to those 100 decimal digits, and does not scale down to 50 or up to 1,000.

---

## Comment 10

> Username: kshegunov  
> Created at: 2021-01-19 09:59:39 UTC  
> Updated at: 2021-01-19 10:00:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/150#issuecomment-762734923  

> A super-easy approach I sometines take is really trivial. I compute a least-squares multiple precision best fit of a curve of tgamma in the range 1...2. This might end up with a polynomial with, say, 64 coefficients giving 100 decimal digits of precision.  
  
I hate to put you down, but while intuitive this is more often than not a dubious approach. LSE is excellent in very many cases, but not here. The least squares guarantee that the error is bound *on average*, but nothing can be said about the maximum error in the interval, and this is a problem ... typically for function evaluations one wants to minimize the maximum error\[[1]\].  
  
[1]: https://en.wikipedia.org/wiki/Remez_algorithm

---

## Comment 11

> Username: ckormanyos  
> Created at: 2021-01-19 10:34:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/150#issuecomment-762754653  

> while intuitive this is more often than not a dubious approach. LSE is excellent in very many cases, but not here. The least squares guarantee that the error is bound on average, but nothing can be said about the maximum error in the interval, and this is a problem...  
  
Thank you for this critical information. I'll integrate those thoughts in future approach(es).

---

## Comment 12

> Username: jzmaddock  
> Created at: 2021-01-19 11:45:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/150#issuecomment-762790282  

>I hate to put you down, but while intuitive this is more often than not a dubious approach. LSE is excellent in very many cases, but not here. The least squares guarantee that the error is bound on average, but nothing can be said about the maximum error in the interval, and this is a problem ... typically for function evaluations one wants to minimize the maximum error[1].  
  
Nod.  And we do have Minimax code in Boost Math we can use, whether it would scale to 100's of digits though is another matter.... often it's a challenge to get up to quad precision.

---

## Comment 13

> Username: kshegunov  
> Created at: 2021-01-19 19:55:21 UTC  
> Updated at: 2021-01-19 19:59:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/150#issuecomment-763090244  

<OT>  
  
> often it's a challenge to get up to quad precision.  
  
Off-topic:  
  
How come? At least for the exponent I haven't had a problem with convergence up to quads (I'd started it from the taylor expansion). I *think*, but don't hold me to it, it's sensitive to the monotonicity of the function in the interval, but that can be chosen appropriately (at least in this case).
