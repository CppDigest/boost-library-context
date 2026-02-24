# #86 A beginning draft for Gauss-Kronrod quadrature. [Closed]

> Username: NAThompson  
> Created at: 2017-08-24 01:44:25 UTC  
> Updated at: 2018-02-10 00:59:24 UTC  
> Closed at: 2017-09-13 16:01:55 UTC  
> Url: https://github.com/boostorg/math/pull/86  

This is just a rough draft for a Gauss-Kronrod quadrature. By running the legendre_stieltjes_example.cpp, you can generate Gauss-Kronrod rules of arbitrary order to 100 digits of precision. A few design questions remain: Do we go to really high order at the expense of compile time? Do we do interval splitting for adaptive quadrature? Do we try more sophisticated error estimation? Do we try to do arbitrary precision or is 100 digits enough?  
  
That's all for today!

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-08-24 10:49:05 UTC  
> Url: https://github.com/boostorg/math/pull/86#issuecomment-324601605  

Nick: I've pushed this to a branch called "gauss" and sent you an invite to be added as a "collaborator" which I think will give you write access... hopefully that will make it easier to collaborate on this?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-08-24 10:49:32 UTC  
> Url: https://github.com/boostorg/math/pull/86#issuecomment-324601706  

On 24/08/2017 02:44, Nick wrote:  
>  
> This is just a rough draft for a Gauss-Kronrod quadrature. By running   
> the legendre_stieltjes_example.cpp, you can generate Gauss-Kronrod   
> rules of arbitrary order to 100 digits of precision. A few design   
> questions remain: Do we go to really high order at the expense of   
> compile time?  
>  
  
Not sure I follow here: I would provide precomputed coefficients for the   
common cases, and common precisions (doubles etc are lot faster to   
initialize than the lexical_cast's, faster to compile as well, but I can   
figure that part out if you want).  I see both QAG/GSL and the advanpix   
source go up to 30/61 so I guess we should probably do so... though I   
believe the smaller ones are better suited to adaptive quadrature?  
  
I'm not sure on what interface is best for this as well... on the one   
hand we don't really need a class (as in tanh_sinh), on the other hand   
if we had:  
  
template <class Real, unsigned GaussOrder>  
class gauss_kronrod { /*details*/ };  
  
Then:  
  
* It's easy to specialize for say gauss_kronrod<double, 7> (for 7,15   
gauss kronrod at double precision).  
* It may be quicker to compile, and result in smaller code, since you   
only compile in the coefficients you're actually using.  
* We could write a short generator program to spit out specializations,   
so if someone really wanted 500 digit precision with 60 gauss nodes and   
all the coefficients pre-computed they can have it.  Default   
specialization would calculate them at runtime (program startup?).  
  
> Do we do interval splitting for adaptive quadrature?  
>  
  
I think so, what I had in mind was:  
  
* Very simple routine for Gauss-only quadrature - rationale: I believe   
it's more accurate for a given number of sample points than   
Gauss-Kronrod, albeit with no error estimate.  
* Very simple routine for one shot Gauss-Kronrod quadrature.  
* More sophisticated routine with interval splitting for adaptive   
quadrature.  
  
I'm not personally that fussed about singularity handling as per QAGS as   
tanh_sinh has that pretty well covered?  
  
> Do we try more sophisticated error estimation?  
>  
  
I don't know.  I know GSL uses some sort of deviation-from-the-mean   
error estimate, but I haven't found any rationale or source reference   
for why they're going to that trouble?  It requires extra storage as   
well as they memorize all the calls to the routine being integrated.  
  
> Do we try to do arbitrary precision or is 100 digits enough?  
>  
  
I guess if we can do it, we can do it?  
  
Best, John.  
  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-08-24 12:32:07 UTC  
> Url: https://github.com/boostorg/math/pull/86#issuecomment-324621535  

Just thinking out loud on the what the right interface should be, does this make any sense to you:  
  
```  
template <class Real, unsigned N>  
class gauss_detail;  
  
template <>  
class gauss_detail<double, 7>  
{  
public:  
   static constexpr std::array<double, 4> const & abscissa()  
   {  
      static constexpr std::array<double, 4> data = {  
         0.000000000000000000000000000000000e+00,  
         4.058451513773971669066064120769615e-01,  
         7.415311855993944398638647732807884e-01,  
         9.491079123427585245261896840478513e-01,  
      };  
      return data;  
   }  
   static constexpr std::array<double, 4> const & weights()  
   {  
      static constexpr std::array<double, 4> data = {  
         4.179591836734693877551020408163265e-01,  
         3.818300505051189449503697754889751e-01,  
         2.797053914892766679014677714237796e-01,  
         1.294849661688696932706114326790820e-01,  
      };  
      return data;  
   }  
};  
/* other specializations... */  
  
template <class Real, unsigned N>  
class gauss : public gauss_detail<Real, N>  
{  
public:  
   typedef double value_type;  
  
   template <class F>  
   static value_type integrate(F f)  
   {  
      value_type result = f(value_type(0)) * weights()[0];  
      for (unsigned i = 1; i < abscissa().size(); ++i)  
      {  
         result += f(abscissa()[i]) * weights()[i];  
         result += f(-abscissa()[i]) * weights()[i];  
      }  
      return result;  
   }  
};  
```  
  
We have a naming problem here as well: so far we have always used "integrate" to mean adaptive integration, but now we have a choice between simple one-shot rules like this, and full adaptive integration, so maybe we need another name for the one shot case?

---

## Comment 4

> Username: NAThompson  
> Created_at: 2017-08-24 18:06:27 UTC  
> Url: https://github.com/boostorg/math/pull/86#issuecomment-324712538  

> I would provide precomputed coefficients for the  
common cases, and common precisions (doubles etc are lot faster to  
initialize than the lexical_cast's, faster to compile as well, but I can  
figure that part out if you want).  
  
I was under impression that `boost::lexical_cast` was evaluated at compile time. If this is incorrect my initial design is fatally flawed.  
  
> * Very simple routine for Gauss-only quadrature - rationale: I believe  
it's more accurate for a given number of sample points than  
Gauss-Kronrod, albeit with no error estimate.  
> * Very simple routine for one shot Gauss-Kronrod quadrature.  
> * More sophisticated routine with interval splitting for adaptive  
quadrature.  
  
I think this is correct.  
  
> I'm not personally that fussed about singularity handling as per QAGS as  
tanh_sinh has that pretty well covered?  
  
Yes, I think the docs should refer the user to `tanh_sinh` for singularities. I'm also under the impression that even after heroic efforts, Gauss-Kronrod simply doesn't do very well on the singularities.  
  
> We have a naming problem here as well: so far we have always used "integrate" to mean adaptive integration, but now we have a choice between simple one-shot rules like this, and full adaptive integration, so maybe we need another name for the one shot case?  
  
Maybe we would use `integrate(F f, Real a, Real b, bool adaptive=true)` for the high level API? Also, we now have two different meanings of 'adaptive': One means 'refine the points in a given interval until tolerance is met', the other is 'split the interval recursively'. Not only that, but there is a way to add points to Gauss-Kronrod quadrature over and over. (I don't know how to do it, but it exists . . .) So we'll have to disambiguate.  
  
>  Do we try to do arbitrary precision or is 100 digits enough?  
I guess if we can do it, we can do it?  
  
Well, unlike the `tanh_sinh` quadrature which rapidly gets limited by the precision of the type, GK doesn't seem to get limited in this fashion, since it cannot nest arbitrarily. We'd have to provide a (say) 500-1001 Gauss-Konrod nodes to be able to use that precision effectively. My vote is if it's easy we should do it.  
  
> I know GSL uses some sort of deviation-from-the-mean  
error estimate, but I haven't found any rationale or source reference  
for why they're going to that trouble?  
  
I read the GSL routine and couldn't make any sense of it. [Notaris](http://etna.mcs.kent.edu/vol.45.2016/pp371-404.dir/pp371-404.pdf) gives a very nice error estimate in equation (3.7). It requires 1) the function be complex analytic, 2) we'd need to know something about the structure of singularities. But if we had an option, say, `entire=true`, then we could give a very precise error estimate.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2017-08-24 18:44:20 UTC  
> Url: https://github.com/boostorg/math/pull/86#issuecomment-324722388  

On 24/08/2017 19:06, Nick wrote:  
>  
>     I would provide precomputed coefficients for the  
>     common cases, and common precisions (doubles etc are lot faster to  
>     initialize than the lexical_cast's, faster to compile as well, but  
>     I can  
>     figure that part out if you want).  
>  
> I was under impression that |boost::lexical_cast| was evaluated at   
> compile time. If this is incorrect my initial design is fatally flawed.  
>  
  
No, very much a runtime routine, it "lexically" converts between 2 types   
by streaming input to a string stream and then reading that into the   
target type.  
  
But we're getting ahead of ourselves here, get the interface right and   
the tests working for say double, and then it's a case of fill in the   
gaps for other types and precisions (or better still just write a short   
program to spit out the tables we need and just paste them into the header).  
  
  
>     We have a naming problem here as well: so far we have always used  
>     "integrate" to mean adaptive integration, but now we have a choice  
>     between simple one-shot rules like this, and full adaptive  
>     integration, so maybe we need another name for the one shot case?  
>  
> Maybe we would use |integrate(F f, Real a, Real b, bool   
> adaptive=true)| for the high level API? Also, we now have two   
> different meanings of 'adaptive': One means 'refine the points in a   
> given interval until tolerance is met', the other is 'split the   
> interval recursively'. Not only that, but there is a way to add points   
> to Gauss-Kronrod quadrature over and over. (I don't know how to do it,   
> but it exists . . .) So we'll have to disambiguate.  
>  
  
That might work, yes.  We can disambiguate in the docs that we're doing   
interval splitting not adding more points to the Gauss/Kronrod (I don't   
think anyone actually does that - seems to be a technical curiosity?)  
  
>     Do we try to do arbitrary precision or is 100 digits enough?  
>     I guess if we can do it, we can do it?  
>  
> Well, unlike the |tanh_sinh| quadrature which rapidly gets limited by   
> the precision of the type, GK doesn't seem to get limited in this   
> fashion, since it cannot nest arbitrarily. We'd have to provide a   
> (say) 500-1001 Gauss-Konrod nodes to be able to use that precision   
> effectively. My vote is if it's easy we should do it.  
>  
  
Or else you just split to ever smaller intervals for high precision.    
Which is better may depend on the function, but we're effectively   
interpolating the function and integrating the interpolation right?  And   
my experience is that interpolations tend to max-out above a certain   
order, and that higher precision approximations are only achievable via   
interval splitting.  
  
>     I know GSL uses some sort of deviation-from-the-mean  
>     error estimate, but I haven't found any rationale or source reference  
>     for why they're going to that trouble?  
>  
> I read the GSL routine and couldn't make any sense of it. Notaris   
> <http://etna.mcs.kent.edu/vol.45.2016/pp371-404.dir/pp371-404.pdf>   
> gives a very nice error estimate in equation (3.7). It requires 1) the   
> function be complex analytic, 2) we'd need to know something about the   
> structure of singularities. But if we had an option, say,   
> |entire=true|, then we could give a very precise error estimate.  
>  
  
OK, sounds like you're on it.  
  
BTW, just had a quick play with a toy recursive interval splitting   
adaptive Gauss-Kronrod, and it's apparent that the termination condition   
is pretty tricky, it *really* wants to go very deep, and the errors   
never get especially low.  Actually, thinking about it now, this could   
be down to cancellation error when rescaling, so it may be that an   
absolute error of epsilon is the best we can ever do at any level?  That   
sort of sucks, but I don't see a solution at present...  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2017-08-31 18:54:19 UTC  
> Url: https://github.com/boostorg/math/pull/86#issuecomment-326389072  

Nick, I hope you don't mind, I've had a go at taking this a bit further, the branch "gauss" now has:  
  
In gauss.hpp, one shot gauss integration:  
  
gauss<Real, Points>::integrate(function, L1);  
  
In gauss_kronrod.hpp, one shot and adaptive Gauss-Kronrod:  
  
gauss_kronrod<Real, Points>::integrate(function, a, b, max_depth, tolerance, error, L1);  
  
when the max_depth is zero, then it's one shot, otherwise it does the usual interval halving.  
  
The adaptive routine is of the "dumb" recursive variety at present, and a better error estimate would be good - I believe you said you had a better formula to hand?  
  
The classes will calculate coefficients to arbitrary precision as required, with specializations providing pre-computed coefficients for the common cases - much work to do there in filling in the blanks.  
  
Testing concentrates on the cases that Bailey identified as working well with Gauss-Kronrod, anything more subtle/difficult behaves fairly badly.  For that we have tanh-sinh, as I understand it the advantage of Gauss-Kronrod is speed (when it works) so I guess we should try to keep things simple?  
  
Best, John.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2017-08-31 21:40:55 UTC  
> Url: https://github.com/boostorg/math/pull/86#issuecomment-326428035  

> I hope you don't mind, I've had a go at taking this a bit further  
  
Of course not. Looks great.  
  
> a better error estimate would be good -  I believe you said you had a better formula to hand?  
  
I'm not sure it's feasible to use the formula that I'd hoped to use. It required the integrand to be complex analytic and have singularities a finite and known distance from the interval `[-1,1]`. I will try to think about this more  . . .  
  
The only thing I worry about is that I didn't implement the fast algorithm for computing nodes and weights of the Gauss-Kronrod rules given by [Laurie](https://doi.org/10.1090/S0025-5718-97-00861-2), the reason being that I thought we could cache them. So I think performance bugs could arise for computing nodes and weights on the fly-although this might be a sufficiently rare case that it shouldn't be a problem?  
  
> Testing concentrates on the cases that Bailey identified as working well with Gauss-Kronrod, anything more subtle/difficult behaves fairly badly. For that we have tanh-sinh, as I understand it the advantage of Gauss-Kronrod is speed (when it works) so I guess we should try to keep things simple?  
  
The tanh-sinh was a heroic effort. Let's steer people towards it in the docs so we don't have to make Gauss-Kronrod perfect for every use case.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2017-09-13 16:01:55 UTC  
> Url: https://github.com/boostorg/math/pull/86#issuecomment-329215303  

Closed as the gauss branch is now merged.

---
