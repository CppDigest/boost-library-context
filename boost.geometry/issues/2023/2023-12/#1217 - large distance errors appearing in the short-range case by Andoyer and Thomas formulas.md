# #1217 - large distance errors appearing in the short-range case by Andoyer and Thomas formulas [Open]

> Username: kkdd  
> Created at: 2023-12-03 04:43:27 UTC  
> Updated at: 2023-12-11 14:22:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1217  

Hello,  
The Boost Geometry's distance calculations by Andoyer and Thomas formulas seem to induce large errors in the short-range case,  as shown in  
Figure 1. The maximum absolute distance error as a function of distance. in [GEODESIC ALGORITHMS: AN EXPERIMENTAL STUDY](https://pdfs.semanticscholar.org/0fd3/7bed6be199ee1766ae46a6ec2ed409d0304c.pdf) by Vissarion Fisikopoulos (2019).  
  
I think they should use [law of haversines](https://en.wikipedia.org/wiki/Haversine_formula#The_law_of_haversines) formula for distance (i.e., d = 2 * asin(sin_d_half)) for accuracy, instead of [spherical law of cosines](https://en.wikipedia.org/wiki/Spherical_law_of_cosines) (i.e., d = acos(cos_d)).  
Thank you.

---

## Comment 1

> Username: kkdd  
> Created at: 2023-12-03 10:12:28 UTC  
> Updated at: 2023-12-04 04:25:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/1217#issuecomment-1837432699  

Hello,  
I will add data set for testing accuracy by using GeodSolve:  
  
```sh  
#!/bin/sh  
step=1  # in degree  
dist_max=19970000  # in meter  
  
for dist_exponent in $(seq 0 7); do  
for dist_significand in 1 3; do  
  dist=$((10 ** dist_exponent * dist_significand))  
  [ $dist -gt $dist_max ] && dist=$dist_max  
  for dir in $(seq 0 $step 90); do  
    for lat in $(seq -90 $step 90); do  
      echo $lat 0 $dir $dist | sh -c 'w="$(cat)"; /bin/echo -n "$w "; GeodSolve -p 9 --input-string "$w"' | awk '{print $1,$2,$3,$5,$6,$7,$4}'  
    done  
  done  
done  
done  
```  
  
The following is an accuracy test using haversine (and above data set):  
![geodistance](https://github.com/boostorg/geometry/assets/5372642/6258eae6-936d-48a4-ba3a-f3770346182b)  
  
The following compares Andoyer formula using haversine with that using cosine law:  
![cosine_law](https://github.com/boostorg/geometry/assets/5372642/684e8faa-dad9-4441-8869-52011d6682dc)

---

## Comment 2

> Username: tinko92  
> Created at: 2023-12-03 12:18:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/1217#issuecomment-1837463698  

If I understand that paper right, it includes the haversine formula in the comparison under the label "spherical" and it produces orders of magnitude larger errors for every maximum distance except 2 meters.

---

## Comment 3

> Username: vissarion  
> Created at: 2023-12-04 10:28:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/1217#issuecomment-1838252936  

Thanks for opening this issue and for the computations. Indeed, it seems a good idea to replace  `d = acos(cos_d))` by `2 * asin(sin_d_half))` in andoyer and thomas formulas.

---

## Comment 4

> Username: kkdd  
> Created at: 2023-12-05 09:24:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1217#issuecomment-1840363287  

I missed the calculation for Andoyer formula in above chart. I calculated, instead, the result of Andoyer-Lambert one, which uses parametric latitude.

---

## Comment 5

> Username: vissarion  
> Created at: 2023-12-05 10:24:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/1217#issuecomment-1840461738  

> I missed the calculation for Andoyer formula in above chart. I calculated, instead, the result of Andoyer-Lambert one, which uses parametric latitude.  
  
Could you please provide the details of how you generated the plots above. I thought you were using the boost geometry's formulas.

---

## Comment 6

> Username: kkdd  
> Created at: 2023-12-05 14:47:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/1217#issuecomment-1840938740  

I'm using the equivalents of the boost geometry's formulas for the plots above.  
I'm now struggling against my disordered status about these, however.  
So please be patient for a while. Thank you.

---

## Comment 7

> Username: kkdd  
> Created at: 2023-12-08 07:29:42 UTC  
> Updated at: 2023-12-11 14:22:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1217#issuecomment-1846683032  

I've just updated https://github.com/kkdd/geodistance-js for providing the benchmarks and plots of formulas. Its minimum deployment is provided in https://kkdd.github.io/geodistance-js:  
  
![andoyer](https://github.com/boostorg/geometry/assets/5372642/677f96d3-b94b-423c-8139-76bf57eae8bf)
