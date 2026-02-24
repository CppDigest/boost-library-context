# #1361 - `ibeta` series fails to converge for large a,b and x close to a/(a+b) [Open]

> Username: JacobHass8  
> Created at: 2026-02-13 21:24:37 UTC  
> Updated at: 2026-02-18 11:03:42 UTC  
> Url: https://github.com/boostorg/math/issues/1361  

When $a, b \gg 1$ and $x \approx a / (a+b)$, the incomplete beta function (`ibeta`) will throw an error because the series expansion doesn't converge. This was first found in https://github.com/scipy/scipy/issues/24566  
  
```C++  
typedef double T;  
  
T a = 3.1622776601699636e+16;   
T b = 3.130654883566682e+18;  
T x = 0.010000000000005001;  
  
std::cout << boost::math::ibeta(a, b, x + 1e-12) << std::endl; // 0.50713  
std::cout << boost::math::ibeta(a, b, x - 1e-12) << std::endl; // 0.49287  
  
// Error in function boost::math::ibeta: Series evaluation exceeded 1000000 iterations, giving up now.  
std::cout << std::setprecision(64) << boost::math::ibeta(a, b, x + 1e-13)<< std::endl;   
return 0;  
```  
The issue is with continued fraction in [here](https://github.com/boostorg/math/blob/c42193db07aef6f69a439815a9310729282587c7/include/boost/math/special_functions/beta.hpp#L860C1-L860C118). I've tried using the `erf` asymptotic expansion [here](https://github.com/boostorg/math/blob/c42193db07aef6f69a439815a9310729282587c7/include/boost/math/special_functions/beta.hpp#L1136C1-L1136C109), but it gave horrible results.   
  
I came up with a tentative solution in Python that is somewhat accurate, but it feels quite hacky. In the asymptotic expansion above ([here](https://github.com/boostorg/math/blob/c42193db07aef6f69a439815a9310729282587c7/include/boost/math/special_functions/beta.hpp#L1136C1-L1136C109)), it solves for   
```math  
\eta^2 = -2 \left( x_0 \ln\left(\frac{x}{x_0}\right) + (1-x_0) \ln\left(\frac{1-x}{1-x_0}\right) \right)  
```  
where $x_0 = a/(a+b)$. Some care needs to be taken to see if $\eta$ is positive or negative, but this is already handled in the implementation. When $x \approx x_0$, we can Taylor expand about $x_0$ to find the approximate solution   
```math  
\eta \approx \frac{x - x_0}{\sqrt{x_0(1-x_0)}}.  
```  
This taylor approximation actually works fairly well for the example above. When more terms are include in the continued fraction, it seems to be converging to the result in the function `ibeta_large_ab` with the taylor approximation for $\eta$.   
  
I only see two issues in regards to using the taylor expansion for $\eta$. First, why does the taylor expansion of $\eta$ give a more accurate result for `ibeta` than using the entire equation for $\eta$? Is it some sort of precision issue with the logs? Second, if we _should_ taylor expand $\eta$, in what regime is this accurate? I found that for $x_0 \pm \delta$ where $\delta < 10^{-13}$ the series fails to converge and the expansion works well. Should $10^{-13}$ just be hard coded into the function [here](https://github.com/boostorg/math/blob/c42193db07aef6f69a439815a9310729282587c7/include/boost/math/special_functions/beta.hpp#L1136C1-L1136C109)?

---

## Comment 1

> Username: JacobHass8  
> Created at: 2026-02-14 01:03:58 UTC  
> Url: https://github.com/boostorg/math/issues/1361#issuecomment-3900351998  

Yes, there's some sort of cancellation going on. For the parameters  
```  
a = 3.1622776601699636e+16  
b = 3.130654883566682e+18  
x = 0.010000000000005001,  
```  
Mathematica is saying that $\eta \approx 1.6.\times 10^{-17}$. However, using the full equation for $\eta$ above, C++ says $\eta \approx 10^{-9}$ (note, the second term proportional to $(1-x_0)\ln(\ldots)$ underflows to 0). Using only the first term in the asymptotic expansion gives $\eta \approx 8 \times 10^{-18}$ which is much closer to Mathematica. I think that for values of $x \approx x_0$ we could use the large a,b code if we implement the approximation for $\eta$ properly.   
  
If this sounds reasonable, I can modify the `ibeta` logic and implement the $\eta$ approximation.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2026-02-14 14:15:38 UTC  
> Url: https://github.com/boostorg/math/issues/1361#issuecomment-3901985216  

Hi @JacobHass8 nice work, yes I think this is the way to go, with regard to when to switch over to the taylor series, then:  
  
We can get more terms for the Taylor series if we need them from https://www.wolframalpha.com/input?i=taylor+series&assumption=%7B%22F%22%2C+%22TaylorSeries%22%2C+%22taylorVariable%22%7D+-%3E%22x%22&assumption=%7B%22C%22%2C+%22taylor+series%22%7D+-%3E+%7B%22Calculator%22%2C+%22dflt%22%7D&assumption=%7B%22F%22%2C+%22TaylorSeries%22%2C+%22taylorFunction%22%7D+-%3E%22c+*+log%28x%2Fc%29+%2B+%281-c%29+*+log%28%281-x%29%2F%281-c%29%29%22&assumption=%7B%22F%22%2C+%22TaylorSeries%22%2C+%22taylorPoint%22%7D+-%3E%22c%22 and also estimate the error in the first omitted term.  So say we go to the (x-x0)^4 term and x-x0 is less than `tools::forth_root_epsilon` then the Taylor expansion should be good to use, otherwise default back to the logs?  
  
And then we just need to figure out a better heuristic for when to use that expansion as currently it lies outside the a ~ b requirements.  It may be that we really need to implement the first few terms of https://dlmf.nist.gov/8.18#E3 to properly fix this though.

---

## Comment 3

> Username: JacobHass8  
> Created at: 2026-02-16 02:20:01 UTC  
> Updated at: 2026-02-16 02:53:20 UTC  
> Url: https://github.com/boostorg/math/issues/1361#issuecomment-3906113668  

> So say we go to the (x-x0)^4 term and x-x0 is less than `tools::forth_root_epsilon` then the Taylor expansion should be good to use, otherwise default back to the logs?  
  
This was actually really easy to implement! For the example above, the remainder term was of order `1e-66` so the Taylor expansion works well.   
   
> And then we just need to figure out a better heuristic for when to use that expansion as currently it lies outside the a ~ b requirements. It may be that we really need to implement the first few terms of https://dlmf.nist.gov/8.18#E3 to properly fix this though.  
  
I'm not sure what you mean by https://dlmf.nist.gov/8.18#E3. My thought was to always use the `erf` approximation. Then we can calculate the remainder term in https://dlmf.nist.gov/8.18#E9, and if it is large, fall back to the continued fraction. Since `a` and `b` are large, we should calculate the log of the remainder to deal with the powers of $(x / x_0)^a$. What do you think about this approach?   
  
For the values above, I calculated the first term in https://dlmf.nist.gov/8.18#E9 is of order `1e-10` so not of the order of machine precision. For such large `a,b` this might not be too bad though since traditional methods don't seem to work well.   
  
Another option is to see if the continued fraction implementation converges, and if not use the erf approximation. I kind of like this because we would always use the more precise method except when it doesn't converge. Plus, the logic would be relatively the same.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2026-02-16 14:03:49 UTC  
> Url: https://github.com/boostorg/math/issues/1361#issuecomment-3908670021  

OK, I don't know how much you want to get into this, but these are the open issues:  
  
1) Calculation of $\eta$ is unstable - now fixed - thanks!  
2) Calculation of the remainder series terms in https://dlmf.nist.gov/8.18#E9 is deeply unstable, if you look at $c_0(\eta)$ in https://dlmf.nist.gov/8.18#E11 then as $\eta \to 0$ we end up subtracting two infinities!  
3) This approximation is only valid for both a,b large and approximately equal in value.  The test case you have above, actually falls outside our current heuristics for when the approximation is valid.  A more rational determination for when the approximation will work could be useful.  For example as $x \to x_0$ we do converge on the correct answer (0.5), so maybe we can extend the usefulness of the approximation depending on how close we are to $x_0$ as well as the relative sizes of a and b, or maybe that gets too complicated!  
4) The other asymptotic approximation (in terms of gamma functions) is for the case where one of a,b is large, or at least they are dissimilar in size... but I'm not sure if the incomplete gamma can handle really large arguments well either, so this may open a whole other can or worms.  
5) There may be a whole hinterland where neither approximation is particularly good.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2026-02-16 14:10:08 UTC  
> Url: https://github.com/boostorg/math/issues/1361#issuecomment-3908697491  

I wonder if I have this right.... in https://dlmf.nist.gov/8.18#E9 the remainder term depends in part on the size of $(\frac{x}{x_0})^a (\frac{1-x}{1-x_0})^b$ and this tends to 1 as $x \to x_0$, so maybe how far away from unity this term is might be a good measure of how well the approximation will work?  Or not ;)

---

## Comment 6

> Username: jzmaddock  
> Created at: 2026-02-16 16:51:07 UTC  
> Url: https://github.com/boostorg/math/issues/1361#issuecomment-3909541830  

Update #1: a quick sanity check, anything that evaluates large powers is doomed to fail at some point, if we have the above power terms evaluated as logs, and x close to x0 then we have:  
  
```  
a * log1p(x - x0) + b * log1p(x0 - x)  
```  
  
with each term < a but similar in magnitude, so when we subtract the two terms (they have opposite sign) and exponentiate we should expect to lose something of the order log10(a) decimal digits of precision.  So for `a` much larger than 1/epsilon we should expect to have few if any digits correct!  
  
The same logic applies if we go via `ibeta_fraction2` as again that involves very large powers of the order `a`.  
  
Update #2: a very rough and ready suggestion for a better check for whether we are in the zone where this expansion would work might be to replace:  
  
```  
(ma / BOOST_MATH_GPU_SAFE_MIN(a, b) < (xa < saddle ? 2 : 15))  
```  
  
with  
  
```  
(ma * fabs(saddle - xa) / BOOST_MATH_GPU_SAFE_MIN(a, b) < (xa < saddle ? 1 : 7.5))  
```  
  
But this is a bit of a guess and would need careful testing and tweaking against our test data.  
  
Update #3: sigh, that still fails the subsequent "powers" check in our heuristics for your test case.... more thinking required!

---

## Comment 7

> Username: JacobHass8  
> Created at: 2026-02-16 19:02:25 UTC  
> Url: https://github.com/boostorg/math/issues/1361#issuecomment-3910099038  

> OK, I don't know how much you want to get into this, but these are the open issues:  
  
2) Yes, the remainder term is quite unstable. We could switch over to using the limiting value in https://dlmf.nist.gov/8.18#E12 when $\eta$ gets small, but then we'd have to worry about $x_0 \rightarrow 0$ or $x_0 \rightarrow 1$ as this would then blow up.   
  
> This approximation is only valid for both a,b large and approximately equal in value.   
3) I think this approximation might be valid for a wider range of a,b. The statement below https://dlmf.nist.gov/8.18#E9 says the approximation is valid for $x \in (0, 1)$ and $a/(a+b), b/(a+b) \in \left[\delta, 1-\delta\right]$ where $\delta \ll 1$ is an arbitrarily small constant. For the example above, $a/(a+b) \approx 0.1$ and $b/(a+b) \approx 0.9$ so $\delta \approx 0.1$. Chances are the approximation gets worse when $a \gg b$ or vice-versa, but maybe a $\delta \approx 0.1$ is okay?

---

## Comment 8

> Username: JacobHass8  
> Created at: 2026-02-16 19:06:23 UTC  
> Updated at: 2026-02-16 19:07:19 UTC  
> Url: https://github.com/boostorg/math/issues/1361#issuecomment-3910122443  

> I wonder if I have this right.... in https://dlmf.nist.gov/8.18#E9 the remainder term depends in part on the size of ( x x 0 ) a ( 1 − x 1 − x 0 ) b and this tends to 1 as x → x 0 , so maybe how far away from unity this term is might be a good measure of how well the approximation will work? Or not ;)  
  
This is actually how I've been thinking about it. When $x = x_0 \pm 10^{-12}$ for large a,b, the continued fraction approach converges. When $x = x_0 \pm \delta$ for some $\delta < 10^{-12}$, the continued fraction doesn't converge, but the erf approximation works well. I'm just not sure why this occurs at $10^{-12}$ and if there's a better criteria for using the erf approximation or not.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2026-02-17 10:27:17 UTC  
> Url: https://github.com/boostorg/math/issues/1361#issuecomment-3913731635  

> I think this approximation might be valid for a wider range of a,b. The statement below https://dlmf.nist.gov/8.18#E9 says the approximation is valid for x ∈ ( 0 , 1 ) and a / ( a + b ) , b / ( a + b ) ∈ [ δ , 1 − δ ] where δ ≪ 1 is an arbitrarily small constant. For the example above, a / ( a + b ) ≈ 0.1 and b / ( a + b ) ≈ 0.9 so δ ≈ 0.1 . Chances are the approximation gets worse when a ≫ b or vice-versa, but maybe a δ ≈ 0.1 is okay?  
  
It's a case of try it and see - we do have test data in this region - in fact I gave it a quick go and got much worse errors, with the worst case being at a,b,x = 1081751633920, 15393168, 0.99998575448989868, but I do remember that the erf approximation worked much better one side of the saddle point than the other, so maybe some tweaking would get that.  
  
>This is actually how I've been thinking about it. When x = x 0 ± 10 − 12 for large a,b, the continued fraction approach converges. When x = x 0 ± δ for some δ < 10 − 12 , the continued fraction doesn't converge, but the erf approximation works well. I'm just not sure why this occurs at 10 − 12 and if there's a better criteria for using the erf approximation or not.  
  
Just tried changing this block https://github.com/boostorg/math/blob/c42193db07aef6f69a439815a9310729282587c7/include/boost/math/special_functions/beta.hpp#L1579-L1589 to:  
  
```  
         if ((ma > 1e-5f / tools::epsilon<T>()) && (fabs(saddle - x) < 1e-12) && (1 - saddle > 0.125))  
               use_asym = true;  
```  
  
and everything passes (based on a very quick test)!  
  
So it looks like you're on to something here!

---

## Comment 10

> Username: jzmaddock  
> Created at: 2026-02-17 16:16:38 UTC  
> Url: https://github.com/boostorg/math/issues/1361#issuecomment-3915641790  

Observation: with a = 10000000272564224, b = 9965820922822656 then the continued fraction works to within 10^-11 of the saddle point when approaching from below and 10^-10 when approaching from above.  Which is to say a 10^-12 cutoff doesn't quite work, but I'm not sure what will.

---

## Comment 11

> Username: JacobHass8  
> Created at: 2026-02-17 18:22:11 UTC  
> Url: https://github.com/boostorg/math/issues/1361#issuecomment-3916404519  

> Observation: with a = 10000000272564224, b = 9965820922822656 then the continued fraction works to within 10^-11 of the saddle point when approaching from below and 10^-10 when approaching from above. Which is to say a 10^-12 cutoff doesn't quite work, but I'm not sure what will.  
  
On a side note, are you able to get Mathematica to return anything sensible for these values of a,b? Mathematica either returns something very large, which surely isn't right, or over/underflows.

---

## Comment 12

> Username: jzmaddock  
> Created at: 2026-02-18 11:03:42 UTC  
> Url: https://github.com/boostorg/math/issues/1361#issuecomment-3920164762  

>On a side note, are you able to get Mathematica to return anything sensible for these values of a,b? Mathematica either returns something very large, which surely isn't right, or over/underflows.  
  
No... and it's a good sanity check that we're in uncharted territory here.  Plus as I said before, the power terms get so large that we risk destroying all the digits in the result anyway.
