# #224 - Argument to jacobi_zeta [Open]

> Username: robertgj  
> Created at: 2019-07-03 15:39:57 UTC  
> Updated at: 2019-07-17 08:22:20 UTC  
> Url: https://github.com/boostorg/math/issues/224  

The DLMF  at Equation 22.16.32 (see : https://dlmf.nist.gov/22.16) defines the Jacobi Zeta function with the argument x. For boost::math::jacobi_zeta the corresponding argument is phi=arcsin(sn(x,k)).

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-07-03 17:21:00 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-508183002  

I'm confused; do you want documentation that conventions are different or do you think this is a bug?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-07-03 17:34:00 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-508187415  

I'm working on a doc update.  
  
Aside: I really really hate these functions, I've found 3 different ways of defining this one so far (NIST, Mathworld, and Carlson).  Ironically Carlson is one of the main authors of the NIST site, but they don't use his definition.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-07-03 20:02:45 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-508235925  

If someone could double check I've got the arguments correct in the above commit that would be great :)  
Did I mention these are confusing???

---

## Comment 4

> Username: pabristow  
> Created at: 2019-07-09 17:19:35 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-509730577  

Yes - I am very confused too!  And I fear I will not be alone :-(  
  
The code has  jacobi_zeta(T1 k, T2 phi)  
  
but  the implementation has the reverse order of phi and k:  
  
T jacobi_zeta_imp(T phi, T k, const Policy& pol)  
  
but the equations all put the order (phi, k)  
  
The test table have the phi value second , for example   
   
static const boost::array<boost::array<T, 3>, 400> jacobi_zeta_big_phi = {{  
      {{ SC_(-1.9808662414550781250e+01), SC_(1.77219090983271598815917968750e-03),  
  
and I presume the test works.  
  
So I think that it needs a BIG warning that the order is confusing  inthat the parameters in the function are reversed from the equations.  
  
(and one might add a link to  https://dlmf.nist.gov/22.16#iii as well)  
  
I presume that making it (phi, k) would be a breaking change and so we can't do that - and would mean recomputing all the tables of test too.  
  
shall I do this?  
  
HTH

---

## Comment 5

> Username: pabristow  
> Created at: 2019-07-10 14:30:37 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-510085984  

I've looked at this a bit more, building a toy example, and now I am > totally_confused.  
  
Naive, as ever, I expected it to follow the Mathworld, http://mathworld.wolfram.com/JacobiZetaFunction.html  
  
so   
  
https://www.wolframalpha.com/input/?i=N%5BJacobiZeta%5B0.5,+0.5%5D,50%5D  
  
N[JacobiZeta[0.5, 0.5],50] =  = 0.11804928579812613667065032016333388271103286022655  
  
but your test value is  
  
       { { SC_(0.5), SC_(0.5), SC_(0.055317014255129651475392155709691519) } },  
  
and how you get to call jacobi_zeta is obfuscated beyond my understanding :-(  
  
I've fumbled with some combinations of 0.5 ^2 and sqrt(0.5) to cater for m = k^2 but I can't reproduce your test data at all.  
  
But you pass your own tests, so you are calculating something ;-)  I feel your hatred ...

---

## Comment 6

> Username: jzmaddock  
> Created at: 2019-07-10 17:17:19 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-510151011  

OK first the argument order - all the elliptic functions follow the same pattern, they use _k_ the elliptic modulus and _phi_ the amplitude.  Both have other representations which are explained here: https://en.wikipedia.org/wiki/Elliptic_integral#Argument_notation.  In particular Mathworld uses _m_ the "parameter" rather than _k_ with _m=k^2_.  
  
For order, all these functions follow the conventions set out by TR1 with _phi_ always the last parameter - this is because it has a special value of pi/2 which TR1 wished to use as the default in C compatible functions.  And yes, this is the opposite way around to the normal mathematical notation.  Internally, in order to keep my brain in one piece, all the functions have their arguments in the same order as the Math, likewise the test data.  
  
So.... the test data for Jacobi zeta has in order, [phi, k, result], so if we take a random row from the test data:  
  
      {{ SC_(1.3784770965576171875000000000000000000000e+00), SC_(1.6117982430230003848548037126420240383595e-15), SC_(2.4369802945405645643758533973163723818628e-31) }},   
  
We have   
phi = 1.3784770965576171875  
k = 1.6117982430230003848548037126420240383595e-15  
  
To calculate this on wolframalpha we need m = k^2, so would input:  
  
JacobiZeta[1.3784770965576171875, 1.6117982430230003848548037126420240383595e-15^2]  
  
Which gives 2.436980294540564564e-31 which matches our test data.  
  
To calculate with our function, we would need to call _jacobi_zeta(k,phi)_ which is to say _jacobi_zeta(1.6117982430230003848548037126420240383595e-15, 1.3784770965576171875)_.

---

## Comment 7

> Username: emsr  
> Created at: 2019-07-10 19:28:23 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-510196070  

On 7/10/19 1:17 PM, jzmaddock wrote:  
>  
> OK first the argument order - all the elliptic functions follow the   
> same pattern, they use /k/ the elliptic modulus and /phi/ the   
> amplitude. Both have other representations which are explained here:   
> https://en.wikipedia.org/wiki/Elliptic_integral#Argument_notation. In   
> particular Mathworld uses /m/ the "parameter" rather than /k/ with   
> /m=k^2/.  
>  
Greetings,  
  
Are you guys using phi instead of u for the Jacobi elliptics sn, cn,   
dn??? It's not common but it would be more consistent.  
  
You can go from one to another: sin(phi) = sn(k,u), phi = am(k,u).  
  
Also, I've been halfway tempted to make narrow types for k, m, m_c, k_c,   
alpha, ... and for phi, u, ... and having overloads.  
  
Ed Smith-Rowland

---

## Comment 8

> Username: jzmaddock  
> Created at: 2019-07-10 19:43:04 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-510200658  

> Are you guys using phi instead of u for the Jacobi elliptics sn, cn, dn??? It's not common but it would be more consistent.  
  
No we're using k and u, see: https://www.boost.org/doc/libs/1_70_0/libs/math/doc/html/math_toolkit/jacobi/jac_over.html though the argument order is the same as the other elliptic integrals (k first, then u).  
  
And yes, named parameters would be a great way to solve this, but I don't think there's anything upcoming in the language that helps, so you're left with as you say, thin wrappers and lots of overloads...

---

## Comment 9

> Username: pabristow  
> Created at: 2019-07-13 08:47:54 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-511103637  

OK – sorry- I’ve finally got my head round this.  I can only cope with one source of confusion at a time ☹  
  
But I doubt if I am the only one, so I propose a ‘Jacobi Zeta for Dummies’ example that I am working up with some working code and some simple comparisons with Wolfram.  
  
In doing this I can confirm that Boost and Wolfram match (apart from the m = k^2 difference).  
  
Encouraged by this, I thought it might be nice to have an cool picture like the one in https://dlmf.nist.gov/22.16#iii  
  
Figure 22.16.3: Jacobi’s zeta function Z(x|k) for 0≤x≤10π and  k=0.4,0.7,0.99,0.99999  
https://dlmf.nist.gov/22.16.F3.mag  
  
but my attempt to replicate this failed, for reasons that I have yet to understand.  My version, and equivalents using Wolfram: Plot[JacobiZeta[z, 0.999999^2], {z, -5, 5}]  
  
https://www.wolframalpha.com/input/?i=Plot%5BJacobiZeta%5Bz,+0.999999%5E2%5D,+%7Bz,+-5,+5%7D%5D  
  
all having the same periodicity and zeros at multiples of pi/2, and a lean to the left rather than the right ?  
  
Can anyone enlighten me on why they are so different?   
  
This is all mega-confusing – a mega-muddle even…  
https://en.wikipedia.org/wiki/Elliptic_integral#Argument_notation is helpful and worth a link,   
but  
https://en.wikipedia.org/wiki/Jacobi_zeta_function is unsatisfactory, and doesn’t refer to the more recent DMLF, nor the main Jacobi article above, and other implementations and versions,  
nor Mathworld that has yet another definition,.  
  
Perhaps we could usefully try to improve this too?  
  
As you observe, without named parameters in C++  (inexplicably, it seems to have disappeared from peoples wish lists for reasons unclear) there are no neat solutions, so I think better documentation and examples are the only options.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2019-07-13 10:15:26 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-511110335  

>Can anyone enlighten me on why they are so different?  
  
I suspect it's because the parameterization is so different: when you change from phi to u that parameter stops being "independent" and gets mixed up with k as well.  
  
>As you observe, without named parameters in C++ (inexplicably, it seems to have disappeared from peoples wish lists for reasons unclear) there are no neat solutions, so I think better documentation and examples are the only options.  
  
Well.... actually named parameters are actually rather easy as it happens, the difficulty is a scoping issue:  in order to write:  
  
```  
jacobi_zeta(u = my_u_value, m = my_m_value);  
```  
  
we would need to GLOBAL variables called _u_ and _m_, and that's not really on.  So we'd be left with something like:  
  
```  
jacobi_zeta(elliptic::u = my_u_value, elliptic::m = my_m_value);  
```  
  
which is not so bad I guess, but still rather clunky :(

---

## Comment 11

> Username: robertgj  
> Created at: 2019-07-13 12:49:24 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-511119685  

For what it's worth, here is some code for Octave that reproduces DLMF Figure 22.16.3 (svg output included)  
[jacobi_Zeta.zip](https://github.com/boostorg/math/files/3388778/jacobi_Zeta.zip)

---

## Comment 12

> Username: robertgj  
> Created at: 2019-07-13 13:25:27 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-511122025  

I guess I ought follow up with an opinion: DLMF Figure 22.16.3 confuses things by showing the argument as a multiple of pi. It seems to me that Jacobi's Zeta function is a special case in which it only makes sense to think of the x argument as a pure number not an angle. The definition of the function makes a call to E with an angle argument arcsin(sn(x,k)) and then subtracts a scaled version of x.

---

## Comment 13

> Username: pabristow  
> Created at: 2019-07-15 12:00:40 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-511373779  

Thanks for both the graph and code to generate, (very pretty ;-) and mostly for confirmation of my suspicion that the graph example was muddying the already very cloudy water.  I conclude that for my example, I should stick to the Wolfram and Boost definition and just provide a graph like this  
  
https://www.dropbox.com/s/6w1o9a63ul192g2/jacobi_zeta_plot.svg

---

## Comment 14

> Username: robertgj  
> Created at: 2019-07-15 12:22:02 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-511379815  

On Mon, 15 Jul 2019 05:00:42 -0700  
"Paul A. Bristow" <notifications@github.com> wrote:  
  
> Thanks for both the graph and code to generate, (very pretty ;-) and  
> mostly for confirmation of my suspicion that the graph example was  
> muddying the already very cloudy water.  I conclude that for my  
> example, I should stick to the Wolfram and Boost definition and just  
> provide a graph like this  
>   
> https://www.dropbox.com/s/6w1o9a63ul192g2/jacobi_zeta_plot.svg  
>   
>   
>   
>    
>   
  
Hi,  
  
I think the problem with the Mathematica definition using an angle is  
that it obscures the periodicity of Jacobi's Zeta in 2K NOT pi. I am not  
sure that the current Boost implementation copes with this.   
  
Cheers,  
  
Rob Jenssen

---

## Comment 15

> Username: emsr  
> Created at: 2019-07-15 17:41:12 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-511499600  

Greetings,  
  
For what it's worth, i am the libstdc++ maintainer of special functions.  I had implemented as a future extension jacobi_zeta(k, phi).  I kept this argument order for consistency with the Lagendre elliptic functions.  I use Carlson's elliptic functions as the implementation for these as I suspect you do as well.  Our results agree dead on (modulo < ulp I think).  
  
All my ruminations are in https://github.com/emsr/tr29124_test.  
  
Anyway, the DLMF plot is confusing.  I'm going to try reproducing it with phi = asin(sn(k,x)).  So Z(k,x) = Z(x|k) = Z(k,phi=asin(sn(k,x))).  Ugh.  An inverse am(k,u) would be nice.  Actually, never mind: x = F(k,phi).  
  
Karney with geographiclib: https://geographiclib.sourceforge.io/ is also a good resource on this stuff.  
  
I think you've got your x and y labels swapped in you svg plot.  
  
I am interested in having our APIs be pretty close in the event we can standardize more of this.  Not that some divergence is insurmountable.  
  
The associated Legendre finctions for |x| > 1 and non-integer degrees and orders is another source of fun.  
  
Best regards,  
Ed Smith-Rowland  
![jacobi_zeta](https://user-images.githubusercontent.com/1936479/61236027-014cdb80-a705-11e9-8f42-d834b47c3b73.png)

---

## Comment 16

> Username: emsr  
> Created at: 2019-07-15 17:49:54 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-511502833  

I missed the Octave code above. Thank you all.

---

## Comment 17

> Username: jzmaddock  
> Created at: 2019-07-15 17:55:33 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-511505005  

>  I had implemented as a future extension jacobi_zeta(k, phi). I kept this argument order for consistency with the Lagendre elliptic functions.  
  
Exactly.  Right or wrong, we have precedent to follow and consistency is important.  Besides the argument forms are all interconvertible.  
  
Nice plot!

---

## Comment 18

> Username: robertgj  
> Created at: 2019-07-15 22:25:28 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-511592812  

Hi,  
  
Yes, the DLMF plot is confusing. I don't know why the x-axis is  
plotted as multiples of pi.  
  
Boost reverses the usual argument orders. I assume to make it easier to  
overload the functions.  
  
Both Boost and Mathematica both seem to start with the historical  
definition of elliptic functions as the inverse of the integration of  
arc-length. In that definition it makes sense to use an angle.  
Whittaker and Watson (First edition 1902 and not without errors!) start  
with the Weierstrass definition of elliptic functions as doubly  
periodic in the complex plane, ie: the period is complex and the  
elliptic functions accept complex arguments. This is more useful for  
conformal mapping applications like filter design that rely on the  
double period. Unfortunately it seems the Boost Jacobian elliptic  
functions do not accept complex arguments.   
  
  
Best regards,  
  
Rob Jenssen

---

## Comment 19

> Username: pabristow  
> Created at: 2019-07-17 08:21:39 UTC  
> Updated at: 2019-07-17 08:22:20 UTC  
> Url: https://github.com/boostorg/math/issues/224#issuecomment-512154071  

[Boost elliptic functions introduction](https://www.boost.org/doc/libs/1_70_0/libs/math/doc/html/math_toolkit/ellint/ellint_intro.html)  
  
says  
  
"This implementation uses k throughout: this matches the requirements of the Technical Report on C++ Library Extensions. However, you should be extra careful when using these functions! "  
  
And k as the first parameter is to match all the other elliptic functions.  
  
So although Boost does not match other implementations, there is some consistency and logic to it.
