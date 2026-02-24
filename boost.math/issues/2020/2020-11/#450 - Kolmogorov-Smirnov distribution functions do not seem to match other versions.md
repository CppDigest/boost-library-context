# #450 - Kolmogorov-Smirnov distribution functions do not seem to match other versions [Closed]

> Username: pabristow  
> Created at: 2020-11-04 17:14:49 UTC  
> Updated at: 2020-11-12 09:46:49 UTC  
> Closed at: 2020-11-12 09:46:49 UTC  
> Url: https://github.com/boostorg/math/issues/450  

I have belatedly found time to study the Kolmogorov-Smirnov distribution more closely and tried to prepare an example of its use.  
  
It seemed sensible to start by trying to reproduce the table of two-sided quantiles, for example in  
  
M J Panik from L H Miller, Tables of Percentage Points of Kolmogorov statistics",  
  
http://www.matf.bg.ac.rs/p/files/69-[Michael_J_Panik]_Advanced_Statistics_from_an_Elem(b-ok.xyz)-776-779.pdf  
Miller LH (1956). Table of Percentage Points of Kolmogorov Statistics." Journal of the American Statistical Association, 51, 111-121.  
  
before trying to reproduce an actual K-S test, perhaps a little like the R KS.test:  
https://stat.ethz.ch/R-manual/R-patched/library/stats/html/ks.test.html  
  
so I started using code like this snippet to output the table:  
`  
   using boost::math::kolmogorov_smirnov_distribution;  
   kolmogorov_smirnov_distribution<> ks1_dist(n); // Default is double.  
   std::cout << n << ", " << alpha << ' ' << quantile(ks1_dist, alpha) << std::endl; //  
`  
but the resulting table didn't match up at all, so I checked some spot values  
`  
  quantile (10, 0.94999999999999996) = 0.42946849874347742  
`  
but the A14 table value for n=10, 1-alpha=0.95 is 0.409  
  
(all other values were a bit near, but all implausibly different, perhaps closer to n=9 than n=10 giving a faint all-too-familiar whiff of an out-by-one error?)  
  
The R ks.test does not compute the pdf, cdf, quantiles etc, so I got the Python equivalent to compute the cdf, pdf quantiles etc from scipy.org  
  
https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.kstwo.html#scipy.stats.kstwo  
  
I surmise that scipy.stats uses algorithms recently reviewed by   
Richard Simard, Pierre L'Ecuyer, Computing the Two-Sided Kolmogorov-Smirnov Distribution  
DOI 10.18637/jss.v039.i11  
  
and/or  the most recent review of the distribution by Paul Mulbregt  
  
https://arxiv.org/pdf/1802.06966.pdf  arXiv:1802.06966v1  [stat.CO]  Paul van Mulbregt MIT 20 Feb 2018  
Computing the Cumulative Distribution Function and Quantiles of the One-sided Kolmogorov-Smirnov Statistic  
Paul van Mulbregt pvanmulbregt@alum.mit.edu February 21, 2018  
`  
   print( kstwo.isf(0.05,10)) gives  0.4092460847775047  
`  
which agrees with the n=10, 1-apha =0.95 in the table A14  for two-sided KS.  
  
   Python 3.9.0 (tags/v3.9.0:9cf6752, Oct  5 2020, 15:34:40) [MSC v.1927 64 bit (AMD64)] on win32  
   Type "help", "copyright", "credits" or "license()" for more information.  
  import numpy as np  
   from scipy.stats import kstwo  
  print(kstwo.ppf(0.95, 10)) outputs 0.4092460847775047 which agrees with the Miller table  
  
So next I tried comparing the cdf and pdf  
``  
    std::cout << "cdf (" << n <<", "  << 0.2 << ") = " << cdf(ks1_dist, 0.2) << std::endl; // cdf (10, 0.20000000000000001) = 0.18137882552899418  
  
    std::cout << "cdf complement (" << n << ", "  << 0.2 << ") = " << cdf(complement(ks1_dist, 0.2)) << std::endl; // cdf complement (10, 0.20000000000000001) = 0.8186211744710058  
  
    std::cout << "pdf (" << n << ' ' << 0.2 << ") = " << pdf(ks1_dist, 0.2) << std::endl; // pdf (10, 0.20000000000000001) = 4.687284794170413  
  
 print(kstwo.cdf(0.2, 10)) outputs 0.25128096000000005  
print(kstwo.pdf(0.2, 10)) outputs 5.134259464902016  
``  
again not absurdly different, but not plausible.  
  
I have now got some Maple functions from LM Leemis that compute (slowly) EXACT CDFs, and have translated a few into C++, with similar disagreement with Boost.Math values :-(  
  
I was not reassured that all the Boost.Math tests that run error free are only testing internal consistency, and no spot values.  
  
Looking under the bonnet soon leads into functions above my pay grade like \boost\math\special_functions\jacobi_theta.hpp  
  
So I am perplexed at what I am doing wrong.  Suggestions most welcome.  
  
@evanmiller @jzmaddock @NAThompson

---

## Comment 1

> Username: evanmiller  
> Created at: 2020-11-05 13:20:20 UTC  
> Url: https://github.com/boostorg/math/issues/450#issuecomment-722373282  

Hi Paul,  
  
Looking at the linked references I am guessing that other implementations use more exact forms of the K-S distribution. The [Boost header file](https://github.com/boostorg/math/blob/develop/include/boost/math/distributions/kolmogorov_smirnov.hpp) lays out the pros and cons of the various available equations and methods. In the interest of simplicity, weighing the various considerations mentioned in the header file, I chose to implement only the 1st order asymptotic form, i.e. the equation which is laid out in Kolmogorov's original paper. For small N, it returns markedly different values than exact or higher-order approximations, as you discovered.  
  
For your testing, I would suggest amping N up to 1,000 or so to see if it starts to converge with the other implementations. If there's no such convergence for large N then this would indicate a bug in the implementation. The CDF wrapper around the Jacobi Theta function is fairly thin, and so the tests basically rely on the accuracy of the Theta functions, which are measured against a smorgasbord of spot values.  
  
The Simard-L'Ecuyer paper looks to be the state of the art for exact and non-exact implementations. It encompasses several different algorithms, switching between them for various values of x and n, and this is probably the algorithm I/we should pursue for a more exact K-S distribution. I will note that the resulting PDF will be fairly complicated, which is why some of the other implementations don't include it, and that the included Durbin and Pomeranz formulas can run very slowly, requiring large matrix powers. I made a note of some implementation issues in the original pull request, with some idle speculation on how the matrix powers might be avoided [here](https://github.com/boostorg/math/pull/422#issuecomment-688384694).  
  
So in summary: discrepancies for small N are to be expected, discrepancies for large N are problematic, and in the future I'd like to close the gap for all N - but this will be a significant undertaking, and may not fit in well with Boost's design goals in terms of speed and external dependencies (some exact implementations require FFT as discussed elsewhere).

---

## Comment 2

> Username: pabristow  
> Created at: 2020-11-05 15:29:18 UTC  
> Url: https://github.com/boostorg/math/issues/450#issuecomment-722450156  

Hmm - annoyingly complicated :-(   
  
For the purposes of implementing the KS test (for what the  rather blinkered KS test is worth), the small n are too inaccurate to be useful?  
  
I've compared n = 10 with n = 1000 (see below) and I'm not sure that I see a big improvement. What do you think?  
  
(The comparison assume that the Python kstwo function is accurate, of course).  
  
`    int n = 10;  
    using boost::math::kolmogorov_smirnov_distribution;  
    kolmogorov_smirnov_distribution<> ks10_dist(n); // Default is double.  
  
    std::cout.precision(std::numeric_limits<double>::max_digits10);  
    std::cout << std::showpoint << std::endl;  
    std::cout << mean(ks10_dist) << std::endl; // 0.27471691419718736  
  
    std::cout << "pdf (" << n << ", " << 0.25 << ") = " << pdf(ks10_dist, 0.25) << std::endl;   
    //  pdf (10, 0.25000000000000000) = 5.1934008312989430  
    // print(kstwo.pdf(0.25, 10)) ==    5.028414299998985  
  
    std::cout << "cdf (" << n << ", " << 0.25 << ") = " << cdf(ks10_dist, 0.25) << std::endl;   
    //                 cdf (10 0.25) = 0.44044028980473621  
    // print(kstwo.cdf(0.25,10)) ==    0.5158884674999997  
    n = 1000;  
    kolmogorov_smirnov_distribution<> ks1000_dist(n); // Default is double.  
    std::cout << "cdf (" << n << ", " << 0.1 << ") = " << cdf(ks1000_dist, 0.1) << std::endl;   
    //          cdf(ks1000_dist, 0.1) = 0.99999999587769273  
     //    print(kstwo.cdf(0.1,1000))=  0.9999999962983148  
    double x = 0.02;  
    std::cout << "cdf (" << n << ", " << x << ") = " << cdf(ks1000_dist, x) << std::endl;   
     // >>> print(kstwo.cdf(0.1,1000))=  
    // cdf (1000, 0.020000000000000000) = 0.18137882552899412  
    // print(kstwo.cdf(0.02,1000)) ==     0.18910283431044228  
    x = 0.03;  
    std::cout << "cdf (" << n << ", " << x << ") = " << cdf(ks1000_dist, x) << std::endl; // >>> print(kstwo.cdf(0.1,1000))=  
    // cdf (1000, 0.029999999999999999) = 0.67089521090218507  
    // print(kstwo.cdf(0.03,1000)) ==     0.67730978560853640  
  
    return 0;  
`

---

## Comment 3

> Username: evanmiller  
> Created at: 2020-11-05 15:45:14 UTC  
> Url: https://github.com/boostorg/math/issues/450#issuecomment-722460002  

The main thing is that the accuracy should improve with larger N. So in absolute terms I'm seeing a delta of 0.07 with N=10 and a delta of 0.007 with N=1000, which is a noticeable improvement. Perhaps try even larger N to see if the pattern continues - you might use the Boost quantile function to identify spot values since the CDF shifts left with larger N.  
  
The suitability at various values of N will ultimately be a judgment call, and we may want to include a louder disclaimer in the documentation about it.

---

## Comment 4

> Username: pabristow  
> Created at: 2020-11-05 17:00:24 UTC  
> Url: https://github.com/boostorg/math/issues/450#issuecomment-722506351  

I think it does  
  
`    n = 10000;  
    kolmogorov_smirnov_distribution<> ks10000_dist(n); // Default is double.  
    x = 0.01;  
    std::cout << "cdf (" << n << ", " << x << ") = " << cdf(ks10000_dist, x) << std::endl;  
    //  cdf (10000, 0.010000000000000000) = 0.73000032832264550  
    // >>> print(kstwo.cdf(0.01,10000))     0.7317808722970808  
      
    n = 100000;  
    kolmogorov_smirnov_distribution<> ks100000_dist(n); // Default is double.  
    x = 0.002;  
    std::cout << "cdf (" << n << ", " << x << ") = " << cdf(ks100000_dist, x) << std::endl;  
    // cdf (100000, 0.0020000000000000000) = 0.18137882552899412  
    // print(kstwo.cdf(0.002,100000))        0.18215916369184584  
`  
  
but even this is not very close to kstwo' approximation :-(  
  
I'll re-read the references to see if I can find some better 'known good' values.

---

## Comment 5

> Username: pabristow  
> Created at: 2020-11-06 12:02:06 UTC  
> Url: https://github.com/boostorg/math/issues/450#issuecomment-723044577  

I have also fired-up the Simard-l'Ecuyer algorithm from #include "KolmogorovSmirnovDist.h"  
and they are shown comparing the three versions below.  
  
The scipy.stats and the  Simard-l'Ecuyer  agree well, Boost.Math version much less so.  As you warned, the difference is much greater at small n.  
  
    std::cout << KScdf(10, 0.25) << std::endl; // 0.51588846750000017  
    //                             kstwo          0.5158884674999997     
    //  boost.math                                0.44044028980473621  
  
    std::cout << KScdf(1000, 0.1) << std::endl; // 0.99999999629831493  
    //                print(kstwo.cdf(0.1,1000))=  0.9999999962983148  
    //                     cdf(ks1000_dist, 0.1) = 0.99999999587769273  
  
    std::cout << KScdf(100000, 0.002) << std::endl; // 0.18215916369184590  
    //           print(kstwo.cdf(0.002,100000))        0.18215916369184584  
    //           cdf (100000, 0.0020000000000000000) = 0.18137882552899412  
`

---

## Comment 6

> Username: pabristow  
> Created at: 2020-11-06 17:37:52 UTC  
> Url: https://github.com/boostorg/math/issues/450#issuecomment-723208398  

It would appear that we could use an exact method for small n (and usefully deal with special cases first of all), but this might leave an ugly discontinuity at the changeover.  
  
I can conceive of re-engineering the various methods used by Simard and l'Ecuyer using Boost.Multiprecision higher precision arithmetic in some places, but it is not obvious to me where the accuracy loss is coming from and so where to hit with more bits.  Using more bits always has a big run-time cost too. There are some license issues too; we'd need to reprogram from the published algorithms.  
  
I also don't see a real use case for high accuracy CDF or quantiles, so this looks a lot of work for little gain.  
  
More informed views?  @jzmaddock @NAThompson

---

## Comment 7

> Username: evanmiller  
> Created at: 2020-11-06 18:16:38 UTC  
> Url: https://github.com/boostorg/math/issues/450#issuecomment-723226976  

> It would appear that we could use an exact method for small n (and usefully deal with special cases first of all), but this might leave an ugly discontinuity at the changeover.  
  
The potential discontinuity issues dissuaded me from including an exact method, even though some values are trivial. The Simard-L'Ecuyer changeover happens not at particular values of n, but at a frontier on the n-x plane, so for certain N there would be a discontinuity in X with implications for the PDF and quantile functions.

---

## Comment 8

> Username: pabristow  
> Created at: 2020-11-09 14:42:44 UTC  
> Url: https://github.com/boostorg/math/issues/450#issuecomment-724056221  

OK, I understand more now and conclude:  
  
What You Get Is What Was Promised (YGIWWP).  
  
1 But there needs to be better, and louder, 'expectation management' - or naive users will plop into the same pit that I just have.  
I would be unsurprised at differences in the 3rd decimal place, but I was alarmed at a difference in the 1st decimal digit!  
If users compare KS-test calculations with other stats packages, they will waste even more time being even more puzzled.  
  
2 I am unclear at a use case for these approximate results when there are many other algorithms that do better?  
Is the USP of this version the free Boost licence?  Or is it a placeholder for a future 'exact' version?  
Or is it fast, or small?  Or just Boost.Math?  Or is using Jacobi theta cool?  
  
3  Most of the rationale for algorithms in Boost.Math is held in a specific Quickbook Rationale section.  
It's not ideal to expect casual users to need to ferret into the namespace detail header file.  
Another section on Accuracy could provide a suitable place for warnings too?  
  
4 Now, I'm not convinced at the value of a worked example of the KS-test,  
especially as most would-be users should get a firm nudge towards the Anderson-Darling test already implemented by @NAThompson.  
[](https://www.boost.org/doc/libs/release/libs/math/doc/html/math_toolkit/anderson_darling.html)  
  
5 Traditionally, Boost.math's mantra has been "First be right, then be Fast", so a future 'more exact' implementation would be excellent.

---

## Comment 9

> Username: evanmiller  
> Created at: 2020-11-09 15:56:53 UTC  
> Url: https://github.com/boostorg/math/issues/450#issuecomment-724102871  

Well, I've given my private rationale for contributing this code elsewhere - the limiting distribution is not useless, it's not trivial, and it's not freely available in C/C++ form. I didn't use Jacobi theta to be "cool" but rather implemented the Jacobi theta functions in order to ensure the accuracy of this limiting distribution. The K-S code being small is a side-effect of me putting most of my time and effort into the Theta functions.  
  
I think the confusion perhaps rightly arises out of the interface design. You'll see in #421 that I originally proposed putting "asymptotic" in the K-S name to be as clear as possible, but I decided instead to parameterize it with N so that small-N implementations could be dropped in later. It's possible that this was a mistake, but I didn't hear objections at the time.  
  
If it's your judgment that end users are currently better served by A-D or by the Python/R implementations, then we can certainly skip the worked example until a more exact implementation is in place. I am also happy to look at any proposed changes to the documentation. For what's worth, I believe that the inaccuracies relative to the exact version will tend to inflate the Type II error rate rather than Type I error rate - while obviously not ideal, this is often acceptable in hypothesis-testing research contexts.

---

## Comment 10

> Username: pabristow  
> Created at: 2020-11-09 18:21:46 UTC  
> Url: https://github.com/boostorg/math/issues/450#issuecomment-724188802  

I'm sorry if 'cool' came over as derogatory - it was't meant - after all it IS COOL!   
  
And leads to small code size, so that is a use case.  
  
I'm only concerned about confusion when the less-informed like me compare with other implementations. But clearer documentation should help.
