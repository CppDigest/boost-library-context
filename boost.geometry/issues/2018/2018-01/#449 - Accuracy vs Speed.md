# #449 - Accuracy vs Speed [Closed]

> Username: cffk  
> Created at: 2018-01-14 19:37:42 UTC  
> Updated at: 2024-10-02 10:48:17 UTC  
> Closed at: 2024-10-02 10:48:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/449  

@awulkiew: This is in response to a comment you made to pull request #431.  I thought it a good idea to split this discussion into a separate issue.  For the record, here is your comment  
  
> @cffk Boost.Geometry is more performance-focused. In most cases where time/accuracy tradeoff has to be made the time is the default choice. So it's actually the other way around than what you suggest. Typicall user of Boost.Geometry would get less accurate result fast and only if something more accurate was needed he/she'd have to pass a different strategy.  
>  
> The practical aspect also comes to mind. AFAIU in practice most of the average users don't need very good accuracy, a few meters is ok or even performing calculations on a sphere. Running the algorithm 30x faster is a good deal though. It all depends on the use-case of course.  
>  
> With that said, more accurate solutions should also be available for users needing them.  
>  
> The formulas are mostly for the internal use and probably will never be documented. The strategies (which may use the formulas) are for the users however geographic strategies are not documented yet because we're still working on them.  
  
It's difficult to characterize what the "typical user" wants.  However, a short time browsing https://gis.stackexchange.com will convince you that a substantial number of potential users only have the slimmest grasp of what they do want.  It is also axiomatic that whatever caveats on the limits of applicability on the approximate formulas are given, many users will use the formulas well outside these limits and have no idea that their results are garbage.  
  
For this reason, if Boost.Geometry does provide an approximate algorithm for some quantity, it's important that it also provide, where feasible, an accurate algorithm (accurate to double-precision round-off, for example).  Except in special cases, I would also recommend that the accurate algorithm be the default one.  
  
The counter-argument is that "surely the users prefer the 30x speed-up of an approximate algorithm".  However this speed-up factor is the product of an unrealistic benchmark.  If the time taken to transform UTM coordinates to geographical coordinates is 2 us, the difference between a 1 us distance calculation and a 0.03 us distance calculation is not so significant.  In addition, the burden of continually checking whether the approximation is good enough in a particular application is an ongoing cost.  
  
Furthermore, many of the approximate algorithms aren't so neatly characterized as "having an accuracy of better than 1 m".  A good example is Vincenty's algorithm for geodesic distance.  For most pairs of points the error in the distance is 0.1 mm.  But for some pairs, the algorithm doesn't converge and Boost returns what is likely to be a garbage result.  Often, Vincenty is forgiven by saying that such antipodal calculations don't occur often.  However even though the failure probability is just 17/1000000 or so, this still means we have 8 failures when computing the distance matrix for 1000 randomly chosen points.  As a result, the triangle inequality is not satisfied for the Vincenty distance, and this precludes using a vantage-point tree to do nearest-neighbor calculations.  
  
P.S. I can't conceive of anyone with a practical application wanting to use the Andoyer or Lambert methods.  These are of historical interest only.

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-01-15 15:04:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-357707581  

> For this reason, if Boost.Geometry does provide an approximate algorithm for some quantity, it's important that it also provide, where feasible, an accurate algorithm (accurate to double-precision round-off, for example).  
  
I agree. This is what strategies are for.  
  
>  Except in special cases, I would also recommend that the accurate algorithm be the default one.  
  
I'm not sure about that. Your statement "users don't know what they want" is a double-way street. If they're not sure about the accuracy that is needed in their app then either low or high accuracy can be questioned.  
  
> However this speed-up factor is the product of an unrealistic benchmark  
  
Why is this benchmark unrealistic? Why did you assume that the user typically perform transformation between calculating something in geographic CS instead of keeping data as geographic?  
  
> Furthermore, many of the approximate algorithms aren't so neatly characterized as "having an accuracy of better than 1 m"  
  
Yes, the antipodal area has to be handled in a special way. We have that planned and if possible want to improve all formulas that way. I agree that this case should be handled as correctly as possible. However this is an edge case. How often the distance is calculated between antipodal points in a real-life app?  
  
> P.S. I can't conceive of anyone with a practical application wanting to use the Andoyer or Lambert methods. These are of historical interest only.  
  
Interesting. My thoughts are exactly the opposite. Maybe that's because I'm comming from the industry (pragmatic) background where a difference between good enough and fast algorithm v.s. very accurate but slower algorithm is the difference in battery life of an embedded device running the app or saved energy (and money) of a computing cloud or more users handled in a unit of time on a webpage which means less servers needed (money saved). Could you point me to an application where double-precision accuracy is required while calculating long distances on the globe?

---

## Comment 2

> Username: cffk  
> Created at: 2018-01-16 23:04:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-358137454  

> I'm not sure about that. Your statement "users don't know what they want" is a double-way street. If they're not sure about the accuracy that is needed in their app then either low or high accuracy can be questioned.  
  
If a user starts with an accurate but slow algorithm he knows (a) what the right answer is and (b) that the algorithm is slow.  
  
If a user starts with a fast but inaccurate algorithm he'll know that his method is fast but he *won't* know whether the results are accurate.  Inevitably he will assume that they are more accurate than they are.  
  
> Why is this benchmark unrealistic? Why did you assume that the user typically perform transformation between calculating something in geographic CS instead of keeping data as geographic?  
  
Because few applications repeatedly call the algorithms in a tight loop.  Lots of other activities are being carried out, typically; I/O, updating the screen, transforming coordinates, etc.  Lots of applications just involve doing a few thousand distance calculations, and then we're just arguing whether it matters that the calculation took 0.05 secs or 0.02 secs.  I suspect that we are violently agreeing with each other here.  Obviously optimizing for speed is specific to each application.  But remember the old saw: "it's easy to speed up a correct program; but difficult to make a fast program correct."  
  
> Yes, the antipodal area has to be handled in a special way. We have that planned and if possible want to improve all formulas that way. I agree that this case should be handled as correctly as possible. However this is an edge case. How often the distance is calculated between antipodal points in a real-life app?  
  
This failure in Vincenty has major implications.  Calling software has to handle the failure or inaccurate results.  Every application needs to worry about the repercussions.   It's only an "edge case" if the application is "enter two points and calculate the distance".  
  
I alluded to an application which will almost certainly trigger the problem.  You have a gazetteer of all the M harbors in the world and you want to provide an application which shows you the harbor closest to a specific point.  A natural way to structure such a calculation is to build up a vantage-point tree (cost = M * log M distance calculations). Then a specific query takes about log M distance calculations.  However the VP tree depends on the triangle inequality holding.  With Vincenty, it doesn't hold if two of the points of a triangle are nearly antipodal. (With M = 1000, you are more-or-less guaranteed to have antipodal harbors.  Besides and specific query point could easily be antipodal to one of the vantage points.)  Your choices are then:  
  
1. ignore the problem and hope no-one notices  
2. do a thorough error analysis and put a specific value on how from closest is the returned harbor.  
3. partition the harbor data into e.g., octants and figure which (few) octants to search.  
4. pick some other method.  
  
None of these choices are very attractive.  
  
> Interesting. My thoughts are exactly the opposite. Maybe that's because I'm comming from the industry (pragmatic) background where a difference between good enough and fast algorithm v.s. very accurate but slower algorithm is the difference in battery life of an embedded device running the app or saved energy (and money) of a computing cloud or more users handled in a unit of time on a webpage which means less servers needed (money saved).  
  
Navigation is an industry where accuracy would trump speed nearly every time.  Let's say you're flying an airliner across the Pacific and following the strategic lateral offset procedure flying 1 NM to the right of the geodesic.  
  
Andoyer has an error of up to 68 m for distances up to 10000 km.  But here you need also to check the error in heading and the deviation between the Andoyer predicted geodesic and the true geodesic and make sure this is considerably less than 1 NM.  
  
The calculations don't have to be done very often maybe 10 times per second so the difference between a 1 us geodesic calculation and a 0.1 us calculation is inconsequential.  And of course the penalty for returning inaccurate results can be fatal.  
  
> Could you point me to an application where double-precision accuracy is required while calculating long distances on the globe?  
  
I would say that full double precision accuracy is needed when calculating any distance on the globe.  With any moderately complicated calculation, let's say a 2D adjustment of a triangulation network.  The input data may well be accurately represented in single precision.  However performing the least squares adjustment reliably typically requires that all calculations on this data be done at full double precision; similarly all distances and angles computed during the adjustment should carried out close to full double precision accuracy.  
  
[W. Kahan, On the Cost of Floating-Point Computation Without Extra-Precise Arithmetic, p. 4](https://people.eecs.berkeley.edu/~wkahan/Qdrtcs.pdf) gives this "rule of thumb"  
  
> Perform all computation in arithmetic with somewhat more than twice as many sig. digits as are deemed significant in the data and are desired in the final results.  
  
For the record, the statement in boost/geometry/strategies/geographic/distance_andoyer.hpp  
  
> Although not so well-known, the approximation is very good: in all cases the results are about the same as Vincenty.  In my (Barend's) testcases the results didn't differ more than 6 m.  
  
is hopelessly misleading.  As noted about the error for geodesics less than 10000 km is 68 m (the corresponding error for Vincenty is 0.1 mm).  Also you might as well include the full citation for Andoyer:  
  
> Henri Andoyer, Formule donnant la longueur de la géodésique joignant 2 points de l'ellipsoïde donnés par leur coordonnées géographiques, Bulletin géodésique 34(1), 77-81 (1932). https://doi.org/10.1007/BF03030136  
  
Many of the URL citations in this file are dead or junk.

---

## Comment 3

> Username: awulkiew  
> Created at: 2018-01-17 17:35:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-358381607  

> I suspect that we are violently agreeing with each other here.  
  
Sure it all depends on the specific use case and where the bootlenecks are. You seem to argue from the navigation standpoint. Have in mind that Boost.Geometry's primary concern is not globe-wide navigation but higher-level geometrical operations like union of multi-polygons in a relatively small area (probably max. a country). Formulas are not exposed to the user. They are very basic building blocks used in the internals of a single operation and they can be called multiple times. E.g. where a user wants to find all polygons (areas) intersecting a linestring (river).  
  
With that said if the difference between andoyer and more accurate formula like vincenty was not great (e.g. <5x, <10x), which AFAIU is the case, then I wouldn't be against changing the default to the most accurate formula. I'd like to hear from the others regarding it. @barendgehrels @vissarion @mloskot do you have any thoughts regarding the issue we're talking about?  
  
> "it's easy to speed up a correct program; but difficult to make a fast program correct."  
  
I don't agree with this sentiment. :) Very often rewriting the code and using different programming paradigm or instead of cpu-bound algorithm using memory-bound algorithm can lead to >10x speedup. And I don't consider it being easy.  
  
I also don't agree with conflating "accuracy" and "correctness".  
  
> This failure in Vincenty has major implications.  
  
I agree that this should be fixed and we're planning to do this from some time. But this is not a matter of accuracy v.s. speed tradeof WRT the formulas used by algorithms by default.  
  
> "I would say that full double precision accuracy is needed when calculating any distance on the globe."  
  
For perspective, in practice of a different industry, Google Maps use spherical SRS and distances. In GIS the segments are relatively short. Often locally-flat approximation is used, etc. It depends on application.  
  
> For the record (...)  
  
Thanks about pointing out outdated or inaccurate info in andoyer. I'll fix it.

---

## Comment 4

> Username: vissarion  
> Created at: 2018-01-18 12:26:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-358631809  

First of all I agree that it would be useful for Boost.Geometry to have an accurate solution for as many as possible implemented algorithms. Having a more accurate version of Vincenty strategy (with correct treatment of nearly antipodal points and  accurate to double-precision round-off) as in GeographicLib would be probably enough for many algorithms that are parametrized with such a strategy.   
  
On the other hand, providing trade-offs between accuracy and speed has some value. I am not an expert in GIS applications but I can imagine possible scenarios that fast and lower accuracy methods could be useful. For example, analysing GPS trajectories in a country or continent where the number of distances to be calculated is large. Note that I am not aiming to describe a real application here but I would like to make a point that even if the marine-navigation application above is a great example for the need of methods that correctly handling nearly antipodal points it is difficult to believe that this is not only one portion of GIS applications.   
  
Therefore, the selection of the default method for direct or inverse geodesic problems is closely connected to the application focus of the library. With current implementations I also verify @awulkiew's numbers that andoyer is ~5, ~10 times faster than Boost.Geometry's vincenty and GeographicLib's inverse computation respectively. However, those numbers are just a flavour of those implementations behaviour since not rigorous experiments have been performed.

---

## Comment 5

> Username: cffk  
> Created at: 2018-01-19 21:01:14 UTC  
> Updated at: 2018-01-20 18:22:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-359087786  

For sure, 5x or 10x speed-ups are enticing.  But users cannot make a decent assessment of the trade-off without some rigorous accounting of the errors in the various methods.  For geographic distance methods, users are interested in both the error in the distance (perhaps as a function of the distance) together with a measure of the maximum deviation of the resulting path from the true geodesic.  
  
I tried to do a comprehensive error analysis for the GeographicLib algorithms.  This is reported in Section 9 of my [arXiv paper](https://arxiv.org/abs/1102.1215).  This used [high-accuracy direct calculations for the WGS-84 ellipsoid](https://doi.org/10.5281/zenodo.32156), which might profitably be used to benchmark other methods.

---

## Comment 6

> Username: awulkiew  
> Created at: 2018-04-16 21:19:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-381753410  

Moving back to this, @barendgehrels do you remember why andoyer formula was used in default geographic distance strategy?

---

## Comment 7

> Username: barendgehrels  
> Created at: 2018-04-18 09:26:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-382324242  

@awulkiew  Andoyer was selected as a good balance between accuracy and speed. So it is quite fast and still (reasonably) precise (but of course not as precise as Vincenty).

---

## Comment 8

> Username: cffk  
> Created at: 2018-04-18 18:43:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-382488870  

@barendgehrels Of course, the definition of "reasonably precise" will depend on the application.  However, in my applications, an error in the distance of 68 m for points 10000 km apart (the accuracy of Andoyer) is unacceptable.  (An error of 1 m might be barely tolerable.)

---

## Comment 9

> Username: barendgehrels  
> Created at: 2018-04-18 21:29:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-382535544  

@cffk I just answered why it was chosen as the default. Of course it completely depends on the application. If you compare it with haversine it is much much better, with same order of speed. Using a more precise algorithm slows speed down but increases precision, maybe a lot. We have to choose a default and that default will not be perfect for all users. I did read some of the discussion above. Yes, it might make sense to select a default which is as precise as we can offer, but slow. But on the other hand we like to offer a library which gives performance, and gives alternatives to tune to users needs. I still think Andoyer is a reasonable choice - as long as we document that there are (much) better (w.r.t. precision) alternatives. We also do not offer high-precision-floating-point-arithmetic as a default, because it is terribly slow (but more precise).

---

## Comment 10

> Username: danpat  
> Created at: 2018-04-23 20:08:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-383705890  

@cffk 1m in 10,000km is a pretty high level of precision.  If you need that kind of precision for real-world calculations, you might want to re-consider using a single spheriod model for the entire length of your geodesic.  The earth is lumpy and bumpy, and over large distances, the effect of local curvature will add up.  I would take a look at what [PROJ4](http://proj4.org/) has to offer in this regard.

---

## Comment 11

> Username: cffk  
> Created at: 2018-04-23 20:31:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-383712832  

@danpat In many applications, quantities are "referred to" the reference ellipsoid.  Thus latitude and longitudes are usually with reference to the ellipsoid and not the geoid; (otherwise they are the "astronomical" latitude and longitude).  Similarly nearly all projections are referenced to the ellipsoid.  Old-style trigonometric adjustments begin with a reduction to the ellipsoid.  And so on...  
  
In this context, it's important to be able to compute geodesic distances accurately on the ellipsoid.  For example, one meter would be a minimum acceptable accuracy for the azimuthal equidistant projection (which is defined in terms of ellipsoidal geodesics); PROJ4's implementation of this projection is accurate to about 0.1 &mu;m over 20000 km.  
  
I realize that this geodesic distance typically does not correspond with the everyday "distance traveled" which, besides dealing with all those lumps and bumps, needs to account for obstacles, a varying "cost of travel", e.g., to account for winds and ocean currents, etc.

---

## Comment 12

> Username: awulkiew  
> Created at: 2018-04-23 21:29:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-383729299  

@cffk For the context. AEQD is the most accurate projection in PROJ4 isn't it? Also the only one using your geodesic formulas from GeographicLib.

---

## Comment 13

> Username: cffk  
> Created at: 2018-04-23 22:30:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-383743571  

Transverse Mercator is also similarly accurate (within 3900 km of the central meridian).  Probably other projections are if they can be expressed in closed form.  Yes AEQD is the only one using the geodesic formulas.  One day, I will add the ellipsoidal gnomonic projection which is also defined in terms of geodesics.

---

## Comment 14

> Username: awulkiew  
> Created at: 2019-01-09 23:22:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-452907323  

This thread is nearly a year old and I think we know everything. So let me explicitly state what the conclusions are and correct me if the following is incorrect.  
  
1. Andoyer formula stays as the default as Boost.Geometry focuses on performance.  
  
2. This issue remains open and is treated it as a suggestion for adding Charles Karney's geodesic formulas into the library. I think most of the work was already done during GSoC2018 but has to be polished before merging.

---

## Comment 15

> Username: cffk  
> Created at: 2019-01-10 15:46:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-453142848  

I recommend providing some data on the accuracy of the Andoyer method (error in distance as a function of distance + similar information for the heading).

---

## Comment 16

> Username: vissarion  
> Created at: 2019-01-23 09:06:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-456723831  

There are some accuracy data for Andoyer method (and other methods) here: https://github.com/vissarion/geometry/wiki/Accuracy-and-performance-of-geographic-algorithms

---

## Comment 17

> Username: kkdd  
> Created at: 2023-12-04 06:53:02 UTC  
> Updated at: 2023-12-04 07:28:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/449#issuecomment-1837942432  

The distance-dependent accuracies of Andoyer and other methods for distance are tested in https://github.com/boostorg/geometry/issues/1217 .  
  
PS. The calculation speed of Andoyer method seems nearly equivalent to that of Thomas based on my observation when running my Javascript implementation. The difference between them is the second order term of flattening, which consumes very little calculation.
