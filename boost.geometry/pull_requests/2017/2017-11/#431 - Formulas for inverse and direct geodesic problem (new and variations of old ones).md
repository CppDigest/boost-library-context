# #431 Formulas for inverse and direct geodesic problem (new and variations of old ones) [Closed]

> Username: vissarion  
> Created at: 2017-11-06 19:11:48 UTC  
> Updated at: 2019-11-04 09:56:08 UTC  
> Closed at: 2019-11-04 09:56:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/431  

### Overview  
  
In this PL the following new formulas and variations of old ones are implemented:  
1. meridian direct formula  
2. andoyer inverse formula variation for parametric latitude  
3. series expansion formula for direct geodesic problem  
4. spherical direct formula  
5. thomas direct formula with 1st order series  
6. thomas inverse formula with 1st order series  
7. inverse elliptic arc length formulas   
7a. from [Thomas'65] (elliptic-Order) given some Order of approximation   
7b. [Tseng'15] (ellipticTseng)  
8. flat earth approximation  
9. lambert formula  
10. alternative andoyer formula  
   
8-10 are based on code for GSoC17 project "Filtering of compare distance predicates" by Ruoyun Jing, Northwest University, China (cf. https://github.com/BoostGSoC17/geometry/wiki).    
  
Apart from general interest those formulas could be used for optimizations in pt-segment distance as well as pt-box, box-box distance.   
  
### Examples  
  
1. Direct geodesic computation for lon=40, lat=40, azimuth=30, distance=10000km. Geographiclib: same results as series 5 in 9.355 secs.  
  
|method  | lon  | lat | azimuth | time(sec) |  
|---|---|---|---|---|  
 spherical |  	178.023358264549 |	41.6191371125257 |	149.179376238475 |	2.633  
*thomas 1st* | 	177.844831863503 |	41.7932261432015 |	149.090214139317 |	3.645  
thomas 2nd | 	177.8448998486 |	41.793310291692 	| 149.090169271878 |	4.026  
vincenty  |	177.844900043621 |	41.7933102050434 |	149.090169318079 |	4.831  
*series 0*  |	177.901073789153 |	41.7228374265491 |	149.127670548938 |	4.761  
*series 1*  |	177.844930331498 |	41.7932725111642 |	149.090189416241 |	5.144  
*series 2*  |	177.844899952327 |	41.7933103192534 |	149.090169257183 |	5.264  
*series 3*  |	177.844900043788 |	41.7933102050352 |	149.090169318083 |	6.129  
*series 4*  |	177.844900043772 |	41.7933102050559 |	149.090169318072 |	7.973  
*series 5*  |	177.844900043772 |	41.7933102050563 |	149.090169318072 |	7.669  
  
2. Direct geodesic computation for meridian starting at (0,0) and distance=1000km. Geographiclib returns 9.04294443634148 in 9.41 secs.  
  
|method   |      lat     |      time(sec)|  
|-----|-----|-----|  
vincenty  	| 9.0429444363299 |	2.80827  
thomas 	| 9.04294437365253 |	1.72716  
*meridian series 0*  |	 8.9982311915998 |	1e-06  
*meridian series 1*  |	 9.0428195432855 	| 0.003463  
*meridian series 2*  |	 9.04294416169439 |	0.003546  
*meridian series 3*  |	 9.04294443615558 |	0.003513  
*meridian series 4*  |	 9.04294443662367 |	0.003529  
  
3. Inverse geodesic computation for lon=40, lat=40, lon=45, lat=45.   
  
| method | distance |azimuth|reverse azimuth| time(sec)|time (sec) - only distance  
|---|---|---|---|---|---|  
*thomas1st* | 	 690633.760235 |	34.8352538976 |	38.2175934124 |	4.10074 | 2.335276  
andoyer   | 	 690637.677592 |	34.8349299084 |	38.2172759010 |	2.633218 | 1.316317   
*andoyer_p* | 	690633.760235 |	- |	- |	-  |2.297484  
thomas   | 	690634.644443 |	34.835303457 |	38.2176494876 |	4.177778  |2.246545  
vincenty  | 	 690634.645488 |	34.8353034166 |	38.2176494413 |	6.332994 | 5.851384  
spherical | 	 690441.284980 |	34.7354067295 |	38.1177406886 | 0.583828 |0.079484  
*elliptic-0* | 	 690517.302199 |	34.8355052945 |	38.2178391678 |	1.814557  | 1.295912  
*elliptic-1* | 	 690635.133352 |	34.8355052945 |	38.2178391678 |	2.144485  | 1.60802  
*elliptic-2* | 	 690634.647406 |	34.8355052945 |	38.2178391678 |	2.531456  | 1.83774   
*elliptic-3* | 	 690634.646825 |	34.8355052945 |	38.2178391678 |	2.771865 | 2.22473  
*elliptic-Tseng* | 689632.701745 |	- |	- |	- |  7.043043  
*flat approx* | 	 697942.741928 |	- |	- | -|	0.060331    
 *lambert*     | 	 691209.64130 |	- |	- | -|	0.605662  
*andoyer2*     | 	 690637.677592 |	- |	- | -|	1.275974  
  
4. Short distances: inverse geodesic computation for lon=10, lat=10, lon=10.1, lat=10.002.   
  
| method | distance |azimuth|reverse azimuth| time(sec)|time (sec) - only distance  
|---|---|---|---|---|---|  
*thomas1st* | 	 10966.1342901 |	88.8354213057 |	88.8527876657 |	3.867777|  3.011785  
  andoyer   | 	 10966.1358699 |	88.8354443842 |	88.8528109251 |	2.368427 | 1.168563    
  *andoyer_p* |  10966.1342906 |	- |	- |	- |2.946217    
  thomas   | 	10966.1343146 |	88.8354332111 |	88.8527997514 |	3.943049  |3.005963  
  vincenty  | 	 10966.1343137 |	88.8354332486 |	88.8527997895 |	3.821359  |3.216909    
  spherical | 	 10952.801900 |	88.8278801897 |	88.8452467306 |	0.462369|0.080687  
  *elliptic0* | 	 10965.5916733 |	88.8354332524 |	88.8527997933 |	1.595881  |0.792151  
 *elliptic1* | 	 10966.1343041 |	88.8354332524 |	88.8527997933 |	1.762004|  1.206682   
 *elliptic2* | 	 10966.134304 |	88.8354332524 |	88.8527997933 |	2.167935 | 1.370632   
 *elliptic3* | 	 10966.134304 |	88.8354332524 |	88.8527997933 |	2.434363  |1.690056  
 *flat approx* | 	 10966.1660730 |	88.8445863054 |	88.8445792874 |	0.667795  |0.062619    
 *lambert*     | 	 10965.0564853 |	- |	- |	-| 0.578585  
*andoyer2*     | 	 10966.1358701 |	- |	- |	-|1.140362  
  
### Comments:  
* Times are for 10M iterations  
* Italics in tables indicate the new methods  
  
*** LAST UPDATE 28 Nov. 2017

---

## Review 1 [Commented]

> Username: cffk  
> Created_at: 2017-12-23 12:08:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/431#pullrequestreview-85422243  

📁 include/boost/geometry/formulas/elliptic_meridian_arc_length.hpp

```diff
 146 |+         return M * (C0 * lat + C2 * sin(2*lat) + C4 * sin(4*lat)
 147 |+                   + C6 * sin(6*lat) + C8 * sin(8*lat) + C10 * sin(10*lat));
 148 |+ 
```

> Username: cffk  
> Created_at: 2017-12-23 12:08:19 UTC  
> Updated_at: 2018-03-13 12:26:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/431#discussion_r158581452  

The Order 5 expression omits the terms  
  
    n^4/16 * lat +  3*n^5/8 * sin(2*lat)  
  
Also why aren't the coefficients given with full precision, e.g., replace  
  
    0.729166667  
  
with  
  
    0.72916666666666667  
  
or better yet by  
  
    CT(35)/CT(48)  
  
Finally, for full accuracy with the SRMmax ellipsoid (f = 1/150), the 6th-order  
series should be used.  Why not include this too?

> Username: vissarion  
> Created_at: 2018-01-05 15:29:15 UTC  
> Updated_at: 2018-03-13 12:26:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/431#discussion_r159902683  

Thank you for the comments. I updated the formula with the higher order terms.


---

## Comment 2

> Username: cffk  
> Created_at: 2017-12-23 14:10:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/431#issuecomment-353727989  

Sorry, I'm coming rather late to this effort.  It looks like a lot of  
added functionality here is overlapping.  Surely this is going to  
confuse the average user who will just expect boost to provide  
best-of-breed routines for meridian arc length, geodesics, etc.  Does  
the average user really need old not-very-good approximate formulas?  
  
Perhaps my concerns would be allayed if I could see the documentation  
for the end-user.  Where is this?

---

## Comment 3

> Username: vissarion  
> Created_at: 2018-01-05 16:58:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/431#issuecomment-355606896  

The contribution of this PR 3-fold. First to add missing functionality (formulas 1,4,5), second to add new formulas that could be potentially useful (formulas 3,7,8) and third to add variations of existing formulas that might be useful (formulas 2,6). The only redundant formula is 9 but I added here only because it was implemented by a GSoC student. I could remove it from the PR and then this PR page would be useful as future reference.   
  
A longer term goal could be to provide time-accuracy trade-offs for distance computation. This needs more extensive experiments. For now the user could be notified that for example formula 8 is very fast but accurate only for short distances. In any case formulas in boost geometry provide low level functionality so the average user should use the distance function to compute distances.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2018-01-11 18:58:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/431#issuecomment-357026521  

@cffk Boost.Geometry is more performance-focused. In most cases where time/accuracy tradeoff has to be made the time is the default choice. So it's actually the other way around than what you suggest. Typicall user of Boost.Geometry would get less accurate result fast and only if something more accurate was needed he/she'd have to pass a different strategy.  
  
The practical aspect also comes to mind. AFAIU in practice most of the average users don't need very good accuracy, a few meters is ok or even performing calculations on a sphere. Running the algorithm 30x faster is a good deal though. It all depends on the use-case of course.  
  
With that said, more accurate solutions should also be available for users needing them.  
  
The formulas are mostly for the internal use and probably will never be documented. The strategies (which may use the formulas) are for the users however geographic strategies are not documented yet because we're still working on them.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2018-01-11 19:25:01 UTC  
> Updated_at: 2018-01-11 19:32:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/431#issuecomment-357034088  

@cffk Btw, the design of Boost.Geometry allows to e.g. use GeographicLib with it. And it wouldn't even be required to write separate strategies. It should be possible by simply wrapping GeographicLib inverse and direct solutions implementations in order to have formulas with interface used by Boost.Geometry strategies and then ~~specializing some traits~~ implementing FormulaPolicy to allow all geographic strategies to use these formulas. The problem is that in some Boost.Geometry formulas there are additional series expansions (e.g. area or geodesics intersection formulas) so it's possible that additional terms would have to be added to be consistent in accuracy with GeographicLib and that would be more complex since it'd require to modify other Boost.Geometry formulas (AFAIR currently series are expanded to max 4 terms). But anyway, it would be doable.

---

## Comment 6

> Username: vissarion  
> Created_at: 2018-02-21 15:11:18 UTC  
> Updated_at: 2018-03-13 11:03:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/431#issuecomment-367357573  

Testing the accuracy and performance of some distance methods using data from here: https://zenodo.org/record/32156#.WOyaMrUmv7C.   
  
For now tests consider the first 100K entries i.e. points randomly distributed on the ellipsoid and entries from 150K to 200K i.e. short distances (<~1km).   
  
Accuracy  
---  
Table rows correspond to methods, columns to segments of maximum length (km) and cells to maximum absolute error (m).    
  
||1|50|100|1000|2000|5000|7500|10000|12500|15000|17500|19000|20000|  
---|---|---|---|---|---|---|---|---|---|---|---|---|---|  
**vincenty**|6.310545444e-06|2.077285899e-08|2.722546924e-06|6.929389201e-06|1.434027217e-05|3.41385603e-05|4.836451262e-05|5.976483226e-05|6.797909737e-05|7.792189717e-05|7.868744433e-05|7.887929678e-05|1059.260239|  
**thomas**|0.1737279|5.503307329e-05|0.000230007543|0.002755686874|0.005525078392|0.01340140309|0.01953940932|0.02994969301|0.09233240411|0.3136218823|1.71884165|48.67808169|927.742111|  
**andoyer**|0.0636499|0.207934641|1.192836701|11.39279713|23.9528954|51.61047244|63.08675041|67.83741458|68.35681641|68.35681641|130.3050551|811.9854736|3090.820303|  
**elliptic-0**|1.8609793|2.162422504|141.1628311|1636.588224|3229.254077|7488.471537|9842.197861|10698.03398|10698.03398|10698.03398|10698.03398|10698.03398|10698.03398|  
**elliptic-1**|1.459575087|0.029380133|0.4103053273|5.388611339|10.71531641|25.07278668|33.83186046|38.8874907|41.12467587|71.16501209|183.1528738|890.2267087|3129.699357|  
**elliptic-2**|1.459575087|4.366122084e-05|0.001192027659|0.01770378056|0.086422113|1.472333112|5.164346168|13.43793933|29.73758777|66.38469973|176.9968409|879.317782|3128.632235|  
**elliptic-3**|1.459575087|1.090847945e-07|5.463807611e-06|0.01040622615|0.08642152185|1.472326002|5.164267235|13.43741994|29.73532316|66.37959886|176.9895445|879.3019077|3128.631758|  
**ell-mer-1**|0.001548128884|0.03274110692|0.03274110692|1.335707043|2.6954533|5.656741893|6.856416455|11.69660518|27.20785655|62.98475838|173.9727914|875.3467778|3128.269629|  
**ell-mer-2**|1.008381105e-06|2.75311686e-06|5.907582818e-06|0.01041171211|0.08647521632|1.47261388|5.164669048|13.43803027|29.73590323|66.3798465|176.9895658|879.3018813|3128.631758|  
**ell-mer-3**|1.043861175e-08|4.855610314e-08|5.449372111e-06|0.01040622732|0.0864215293|1.472325803|5.164267053|13.43741941|29.73532026|66.37959105|176.9895329|879.3018771|3128.631758|  
**ell-mer-4**|1.079649792e-08|2.992965165e-08|5.445646821e-06|0.01040622732|0.0864215591|1.472325998|5.164267194|13.43741949|29.73532024|66.37959108|176.989533|879.3018773|3128.631758|  
**flat**|232.5010385|160.0835061|160.0835061|479533.4729|1643653.41|6148698.833|10914467.79|11753732.89|11753732.89|11753732.89|11753732.89|11753732.89|11753732.89|  
**spherical**|6.163784662|5.273273452|456.4243049|5429.289324|10771.94749|25205.28486|33689.95013|37575.32335|37852.16316|37852.16316|37852.16316|37852.16316|37852.16316  
  
  
Performance  
---  
Mean time per method (1000 iterations)  
  
| method | time(sec)  
---|---|  
**vincenty** | 5.38E-04  
**thomas** | 2.57E-04  
**andoyer** | 1.27E-04  
**elliptic-0** | 7.80E-05  
**elliptic-1** | 1.22E-04  
**elliptic-2** | 1.57E-04  
**elliptic-3** | 1.98E-04  
**ell-mer-1** | 2.02E-04  
**ell-mer-2** | 2.31E-04  
**ell-mer-3** | 2.75E-04  
**ell-mer-4** | 3.19E-04  
**flat** |6.28E-06  
**spherical** | 1.53E-05

---

## Comment 7

> Username: cffk  
> Created_at: 2018-02-21 17:36:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/431#issuecomment-367407188  

Thanks for the accuracy + timing data!  
  
Since computing great elliptic distances is essentially the same problem  
as computing distances along the meridian (they are both computing  
distance on an ellipse), it make senses for them to use the same method  
and to share the same code.  (Calculating geodesic distances via the  
method of Bessel + Helmert also involves computing distances on ellipses  
on the auxiliary sphere.  So you will find this calculation buried in  
Vincenty's method -- although he did a good job hiding this.)  
  
My recommended method of computing the distance is Bessel's series using  
the parametric latitude:  
  
https://en.wikipedia.org/wiki/Meridian_arc#Series_in_terms_of_the_parametric_latitude  
  
The series in terms of the parametric latitude converges substantially  
faster than in terms of the geographical latitude.  Compare the mu-beta  
and mu-phi entries in the table at  
  
https://geographiclib.sourceforge.io/html/auxlat.html#auxlaterror  
  
Incidentally, notice how bad it is to use e^2 (the choice in Tseng15) as  
the small parameter.  
  
I use Horner's method to compute the coefficients of the trigonometric  
series and Clenshaw method to sum the trigonometric series.  The latter  
method minimizes the evaluation of trigonometric functions which will  
presumably speed up the code.  
  
I've only implemented great ellipse calculations in MATLAB.  See  
  
https://sourceforge.net/p/geographiclib/code/ci/release/tree/matlab/geographiclib/gedistance.m  
  
This calls functions C1f and A1m1f (which are also used to geodesic  
distances) to compute the coefficients.  SinCosSeries does the Clenshaw  
summation.

---

## Comment 8

> Username: vissarion  
> Created_at: 2018-02-23 15:30:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/431#issuecomment-368041372  

> Since computing great elliptic distances is essentially the same problem  
as computing distances along the meridian (they are both computing  
distance on an ellipse), it make senses for them to use the same method  
and to share the same code. (Calculating geodesic distances via the  
method of Bessel + Helmert also involves computing distances on ellipses  
on the auxiliary sphere. So you will find this calculation buried in  
Vincenty's method -- although he did a good job hiding this.)  
>  
>My recommended method of computing the distance is Bessel's series using  
the parametric latitude:  
>  
>https://en.wikipedia.org/wiki/Meridian_arc#Series_in_terms_of_the_parametric_latitude  
  
Thanks for the comments! I implemented a version of the formula that uses the meridian arc formula and did some tests (see `ell-mer-` in the updated tables above). It seems similar to existing ones with less variation on times and accuracy. An exception is the short distances (<1km) where the new formula is significantly more accurate.    
  
>I use Horner's method to compute the coefficients of the trigonometric  
series and Clenshaw method to sum the trigonometric series. The latter  
method minimizes the evaluation of trigonometric functions which will  
presumably speed up the code.  
  
I do not have specific data to share here but in my case (order `<=5`) Clenshaw summation was (much) slower that the explicit implementation (probably because the series are short).

---

## Comment 9

> Username: cffk  
> Created_at: 2018-02-23 20:59:59 UTC  
> Updated_at: 2018-02-25 02:59:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/431#issuecomment-368136949  

I'm surprised that you find Clenshaw slower than a straight series.  Here's a simple test `clenshawtest.cpp` showing that Clenshaw is faster for order >= 2.  
  
    #include <cmath>  
    #include <iostream>  
    #include <chrono>  
  
    using namespace std;  
  
    double TrigSum1(double x, const double c[], int n) {  
      // Evaluate  
      //   y = sum(c[i] * sin(i * x), i, 1, n)  
      // using Clenshaw summation.  N.B. c is (n+1) long with c[0] unused.  
      // Operation count = 2 trig, (n + 2) mult, + (2 * n) add.  
      c += n+1;                     // Point to one beyond last element  
      double  
        ar = 2 * cos(x),  
        y0 = n & 1 ? *--c : 0, y1 = 0; // accumulators for sum  
      // Now n is even  
      n /= 2;  
      while (n--) {  
        // Unroll loop x 2, so accumulators return to their original role  
        y1 = ar * y0 - y1 + *--c;  
        y0 = ar * y1 - y0 + *--c;  
      }  
      return sin(x) * y0;  
    }  
  
    double TrigSum2(double x, const double c[], int n) {  
      // Evaluate  
      //   y = sum(c[i] * sin(i * x), i, 1, n)  
      // using plain summation.  N.B. c is (n+1) long with c[0] unused.  
      // Operation count = n trig, (2 * n) mult, + n add.  
     double s = 0;  
      c += n;                       // Point to  last element  
      while (n)  
        s += *c-- * sin(n-- * x);  
      return s;  
    }  
  
    int main() {  
      // Here are the C_1 coefficients for eps = 0.1 up to order 10.  See Eq. (14)  
      // of https://arxiv.org/abs/1102.1215  
      double c[] = {0, -0.04981281157299805, -6.218793774398804E-4,   
                    -2.071629162597657E-5, -9.707098022460943E-7,  
                    -5.434606933593752E-8, -3.396018409729006E-9,  
                    -2.286791120256698E-10, -1.625595092773439E-11,   
                    -1.212226019965279E-12, -9.273529052734385E-14};  
      double pi = atan2(0.0, -1.0);  
      int num = 10000000;  
      double d = pi/num;  
      for (int order = 1; order <= 10; ++order) {  
        double s1 = 0, s2 = 0, dt1, dt2;  
        {  
          auto t0 = std::chrono::high_resolution_clock::now();  
          for (int i = 0; i < num; ++i)  
            s1 += TrigSum1((i + 0.5) * d, c, order);  
          auto t1 = std::chrono::high_resolution_clock::now();  
          dt1 = double(std::chrono::duration_cast<std::chrono::nanoseconds>  
                       (t1 - t0).count());  
          dt1 /= num;  
        }  
        {  
          auto t0 = std::chrono::high_resolution_clock::now();  
          for (int i = 0; i < num; ++i)  
            s2 += TrigSum2((i + 0.5) * d, c, order);  
          auto t1 = std::chrono::high_resolution_clock::now();  
          dt2 = double(std::chrono::duration_cast<std::chrono::nanoseconds>  
                       (t1 - t0).count());  
          dt2 /= num;  
        }  
        cout << "Order = " << order << "\n"  
             << "  sums = " << s1 << " " << s2 << "\n"  
             << "  times = " << dt1 << " " << dt2 << "\n"  
             << "  Clenshaw speed up = " << dt2/dt1 << " times\n";  
      }  
    }  
  
compiling this with  
  
    g++ -O3 -o clenshawtest clenshawtest.cpp  
  
gives  
  
    Order = 1  
      Clenshaw speed up = 0.615403 times  
    Order = 2  
      Clenshaw speed up = 1.23802 times  
    Order = 3  
      Clenshaw speed up = 1.90169 times  
    Order = 4  
      Clenshaw speed up = 2.36802 times  
    Order = 5  
      Clenshaw speed up = 2.99164 times  
    Order = 6  
      Clenshaw speed up = 3.23272 times  
    Order = 7  
      Clenshaw speed up = 3.78895 times  
    Order = 8  
      Clenshaw speed up = 3.87353 times  
    Order = 9  
      Clenshaw speed up = 4.37685 times  
    Order = 10  
      Clenshaw speed up = 4.24539 times

---

## Comment 10

> Username: vissarion  
> Created_at: 2019-10-31 16:24:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/431#issuecomment-548456510  

I guess this is not needed open any more.

---

## Comment 11

> Username: awulkiew  
> Created_at: 2019-10-31 17:17:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/431#issuecomment-548479942  

@vissarion None of these formulas would be useful for us? Even for testing in the future? How about e.g. optimization of meridian distances in other algorithms like `perimeter` of geographic bounding box or distance between geographic box and a point or other box, etc.?

---

## Comment 12

> Username: vissarion  
> Created_at: 2019-11-04 09:55:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/431#issuecomment-549283823  

Few formulas from here have been cherry picked and added to library *e.g.* see   
meridian, spherical and first order direct formulas:   
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/meridian_direct.hpp https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/spherical.hpp#L232 https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/thomas_direct.hpp#L115

---
