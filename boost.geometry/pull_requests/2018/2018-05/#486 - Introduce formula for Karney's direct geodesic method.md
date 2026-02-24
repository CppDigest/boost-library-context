# #486 Introduce formula for Karney's direct geodesic method [Merged]

> Username: adl1995  
> Created at: 2018-05-31 15:10:30 UTC  
> Updated at: 2018-07-25 12:59:49 UTC  
> Merged at: 2018-07-25 12:59:49 UTC  
> Closed at: 2018-07-25 12:59:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/486  

This pull request introduces the direct geodesic method as proposed by [Karney (2011)](https://arxiv.org/pdf/1109.4448.pdf). The following are the highlights of the changes introduced:  
- Implementation of the direct geodesic method.  
- Series expansions of integrals given in the paper. All of these were calculated using Maxima with the [geod.mac](https://sourceforge.net/p/geographiclib/code/ci/release/tree/maxima/geod.mac) script (associated with GeographicLib), except for co-efficients of C3. I noticed there was a variation in the results produced by GeographicLib and the function generated through Maxima, which caused `lon2` to be inaccurate. Replacing the function with [`Geodesic::C3coeff()`](https://sourceforge.net/p/geographiclib/code/ci/release/tree/src/Geodesic.cpp#l1442) from GeographicLib removed this inaccuracy, so I have used that instead.  
- Utility functions in [math.hpp](https://github.com/BoostGSoC18/geometry/blob/feature/geodesic_direct/include/boost/geometry/util/math.hpp), such as normalization, Clenshaw's summation, and Horner's evaluation. Some of these overlap with [formulas/area_formulas.hpp](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/area_formulas.hpp). I will merge them in a single file as part of a separate PR.  
- Test cases with existing dataset and antipodal points dataset, collected from [GeodTest](https://zenodo.org/record/32156), associated with GeographicLib. These were converted into C++ array format using this [Python script](https://github.com/adl1995/boost-geometry-extra/blob/master/geographicLib-dataset-parse.py). Geodesic scale (M12) is missing from the GeodTest dataset, so I generated it manually using this [C++ script](https://github.com/adl1995/boost-geometry-extra/blob/master/geographicLib-direct-antipodal.cpp).  
  
However, the tests fail when comparing geodesic scale (M12) with Boost implementation of the direct method. Also, I noticed that using the C++ header `GeographicLib/Geodesic.hpp` and `GeodSolve` CLI tool both provided different results. As an example, using these points:  
  
```  
lat1: 31.863784754278001, lon1: 0, azi1: 29.572313410210999, s12: 19993324.8682601  
```  
with GeographicLib, GeodSolve CLI, and Boost gave the following results:  
  
```  
GeographicLib version:      -0.99598315084972932  
GeodSolve tool:             -0.99588800900114727  
Boost version:              -0.99625199787331664  
```  
  
I'm not sure why this happens, because `GeodSolve` uses the same C++ code at the back-end. The difference might not seem much, but since only a tolerance within `1e-7` is acceptable, these tests fail.

---

## Review 1 [Commented]

> Username: vissarion  
> Created_at: 2018-06-04 11:20:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-125531074  

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
  34 |+ template <
  35 |+     typename CT,
  36 |+     size_t SeriesOrder = 8,
```

> Username: vissarion  
> Created_at: 2018-06-04 11:20:07 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r192706831  

maybe place it at the end of parameters' list to be more consistent with similar strategies

> Username: adl1995  
> Created_at: 2018-06-04 14:17:54 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r192756207  

Thanks! Just updated this.


---

## Review 2 [Commented]

> Username: vissarion  
> Created_at: 2018-06-04 11:25:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-125532450  

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
  94 |+         Azi const azi12 = math::normalize_angle<CT>(azimuth12);
  95 |+ 
  96 |+         if (math::equals(distance, Dist(0)) || distance < Dist(0))
```

> Username: vissarion  
> Created_at: 2018-06-04 11:25:20 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r192707998  

why not creating a const for 0?

> Username: adl1995  
> Created_at: 2018-06-04 14:18:00 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r192756241  

Thanks! Just updated this.


---

## Review 3 [Commented]

> Username: vissarion  
> Created_at: 2018-06-04 11:47:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-125538549  

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 245 |+         }
 246 |+ 
 247 |+         if (BOOST_GEOMETRY_CONDITION(CalcQuantities))
```

> Username: vissarion  
> Created_at: 2018-06-04 11:47:33 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r192713137  

this part of code duplicates code from https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/formulas/differential_quantities.hpp is it possible to integrate ?

> Username: adl1995  
> Created_at: 2018-06-04 14:20:20 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r192757046  

I actually tried using that code. However, almost all the test cases fail if I do. I find that odd, since it seems to be working well with Vincenty and Thomas. I will try and dig a little deeper into the code and see if I'm missing out on a step.


---

## Review 4 [Commented]

> Username: vissarion  
> Created_at: 2018-06-04 12:09:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-125544878  

📁 include/boost/geometry/util/series_expansion.hpp

```diff
  14 |+ namespace boost { namespace geometry { namespace series_expansion {
  15 |+ 
  16 |+     /*
```

> Username: vissarion  
> Created_at: 2018-06-04 12:09:24 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r192718631  

there is a lot of duplication in the maxima scripts inside comments, could you try to decrease it?

> Username: adl1995  
> Created_at: 2018-06-05 04:31:03 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r192942925  

I just removed the duplicated code in [this](https://github.com/boostorg/geometry/pull/486/commits/cc19342b4eac30183fd6f40d4d4d0a8b36fed885) commit. Please let me know if I should further reduce the comments.

> Username: vissarion  
> Created_at: 2018-06-05 07:25:17 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r192970960  

It could be more useful to have all maxima script in one place. Because for example if someone want to reproduce series for `I2` then the definition of `ataylor` is missing.  
  
Also the following script (taken from area formula, right?) is repeated 7 times!  
  
`To replace each number x by CT(x) the following  
     script can be used:  
       sed -e 's/[0-9]\+/CT(&)/g; s/\[CT/\[/g; s/)\]/\]/g;  
               s/case\sCT(/case /g; s/):/:/g; s/epsCT(2)/eps2/g;'`  
  
Finally, is the script taken from http://geographiclib.sourceforge.net/html/geod.mac without modifications? If so you can just add the link in the comments.

> Username: adl1995  
> Created_at: 2018-06-05 08:07:09 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r192981474  

> Also the following script (taken from area formula, right?) is repeated 7 times!  
...  
  
Yes, it matches the one from area formula, except the last part `s/epsCT(2)/eps2/g;`. Before that the script was converting `eps2` -> `epsCT(2)`. So, I will just keep a single copy of this script and remove the duplicated ones.  
  
> Finally, is the script taken from http://geographiclib.sourceforge.net/html/geod.mac without modifications? If so you can just add the link in the comments.  
  
The scripts are indeed taken from (http://geographiclib.sourceforge.net/html/geod.mac), however, I had to make minor adjustments e.g. change the function header and data types to keep it consistent with Boost Geometry guidelines. Is it okay to discard those changes? If so, I will only provide a link to `geod.mac`, otherwise, I will add them in a separate file.

> Username: vissarion  
> Created_at: 2018-06-05 08:31:15 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r192988237  

>The scripts are indeed taken from (http://geographiclib.sourceforge.net/html/geod.mac), however, I had to make minor adjustments e.g. change the function header and data types to keep it consistent with Boost Geometry guidelines. Is it okay to discard those changes? If so, I will only provide a link to geod.mac, otherwise, I will add them in a separate file.  
  
Ok, since they contain changes, I think it is useful to keep them in Boost.Geometry.

> Username: adl1995  
> Created_at: 2018-06-05 12:28:44 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r193052845  

I have moved the Maxima scripts to: [doc/other/maxima/geod.mac](https://github.com/BoostGSoC18/geometry/blob/feature/geodesic_direct/doc/other/maxima/geod.mac).


---

## Comment 5

> Username: vissarion  
> Created_at: 2018-06-04 12:21:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-394335338  

Thanks for this PR!  
  
> Series expansions of integrals given in the paper. All of these were calculated using Maxima with the geod.mac script (associated with GeographicLib), except for co-efficients of C3. I noticed there was a variation in the results produced by GeographicLib and the function generated through Maxima, which caused lon2 to be inaccurate. Replacing the function with Geodesic::C3coeff() from GeographicLib removed this inaccuracy, so I have used that instead.  
  
lon2 is inaccurate with respect to the data set in https://zenodo.org/record/32156 ? How do you know that Geodesic::C3coeff() is more accurate than the function generated from Maxima?  
  
 > Test cases with existing dataset and antipodal points dataset, collected from GeodTest, associated with GeographicLib. These were converted into C++ array format using this Python script. Geodesic scale (M12) is missing from the GeodTest dataset, so I generated it manually using this C++ script.  
  
Could you please add this information in the code as a comment for future reference?

---

## Comment 6

> Username: adl1995  
> Created_at: 2018-06-05 05:23:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-394585482  

@vissarion Thank you for your comments.  
  
> lon2 is inaccurate with respect to the data set in https://zenodo.org/record/32156 ? How do you know that Geodesic::C3coeff() is more accurate than the function generated from Maxima?  
  
Yes, I compared `lon2` with the dataset in (https://zenodo.org/record/32156). The way I confirmed this was by running the tests. Using the Maxima generated function, around 35 test cases fail due to `lon2` being outside the accepted tolerance value (`1e-7`).  
  
The values returned using the `Geodesic::C3coeff()` function are:  
```  
0.01483154296875, 0.020524211136585777948, 0.023473593522643630693, 0.03908878180363212218, 0.046953669026607428028, 0.124999647527361743, 0.24958019490340407898, 0.01141357421875, 0.01319773757405523322, 0.019537787425094418903, 0.023450519665361754806, 0.046822392333655975249, 0.062342661206936086926, 0.00848388671875, 0.011717110136341421858, 0.013667431352136641745, 0.023393726366666315469, 0.025963026618192931033, 0.00775146484375, 0.0087802482328351444396, 0.013639068088904740192, 0.013626013859041151768, 0.00604248046875, 0.0087644645451213330778, 0.0081736487595328952832, 0.00604248046875, 0.005350800437225099182, 0.0037405831473214285095  
```  
  
Whereas, the values I get with the Maxima generated function [code_C3.txt](https://github.com/boostorg/geometry/files/2070906/code_C3.txt) are:  
  
```  
0.24958019490340407898, 0.124999647527361743, 0.046953669026607428028, 0.03908878180363212218, 0.023473593522643630693, 0.020524211136585777948, 0, 0.062342661206936086926, 0.046822392333655975249, 0.023450519665361754806, 0.019537787425094418903, 0.01319773757405523322, 0, 0.025963026618192931033, 0.023393726366666315469, 0.013667431352136641745, 0.011717110136341421858, 0, 0.013626013859041151768, 0.013639068088904740192, 0.0087802482328351444396, 0, 0.0081736487595328952832, 0.0087644645451213330778, 0, 0.005350800437225099182, 0, 0  
```  
  
> Could you please add this information in the code as a comment for future reference?  
  
Sure, I just added this [here](https://github.com/boostorg/geometry/pull/486/commits/a10815366a58d1dd058dd0447843fbac89327b37).

---

## Comment 7

> Username: vissarion  
> Created_at: 2018-06-05 07:31:42 UTC  
> Updated_at: 2018-06-05 07:31:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-394610560  

>Yes, I compared lon2 with the dataset in (https://zenodo.org/record/32156). The way I confirmed this was by running the tests. Using the Maxima generated function, around 35 test cases fail due to lon2 being outside the accepted tolerance value (1e-7).  
>The values returned using the Geodesic::C3coeff() function are:  
>...  
  
My question is about the place where the error occurs? Is it an error in the dataset or comes from the Maxima script and BG implementation. Is it a case of antipodal points? It seems that there is a big difference in the results so it should be simple to identify which result (dataset/GeographicLib or Maxima/Boost.Geometry) is correct. Could you please paste the link to a complete failing test case (lon,lat,distance,azimuth,etc..)?

---

## Comment 8

> Username: adl1995  
> Created_at: 2018-06-05 11:21:36 UTC  
> Updated_at: 2018-06-09 10:59:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-394673339  

Quoting my previous comment:  
  
> ~Yes, I compared lon2 with the dataset in (https://zenodo.org/record/32156).~  
  
Sorry, I just checked again and it seems I confused the two datasets. The inaccuracy in `lon2` is only with dataset/Boost.Geometry. The tests for antipodal points (collected from dataset/GeographicLib) pass regardless of which function is used (i.e. the one generated through Maxima script or [Geodesic::C3coeff()](https://sourceforge.net/p/geographiclib/code/ci/release/tree/src/Geodesic.cpp#l1442)).  
  
However, for dataset/Boost.Geometry, the tests for `lon2` fail (36/88 cases) if I use the Maxima generated function. I have pasted the failing test case here: https://gist.github.com/adl1995/9a9f5dada2b6a083b7b1d79ae35c3ca3  
  
[line#197 in karney_direct.hpp](https://github.com/BoostGSoC18/geometry/blob/feature/geodesic_direct/include/boost/geometry/formulas/karney_direct.hpp#L197) is responsible for the error. As an example, the points:  
```  
lon1 = 1, lat1 = 1, s12 = 250000, azi12 = -45   
```  
give the following results for `lon2`:  
```  
GeographicLib:        -0.589430658212281  
Boost:                -0.589432892643727  
Test case (expected): -0.589430658212280  
```   
Also, if I replace the `Geodesic::C3coeff()` function in GeographicLib with the one generated through Maxima, I get the exact result provided through Boost (inaccurate). This leads me to conclude that the issue is indeed with the Maxima function.  
  
PS. the values returned by the `Geodesic::C3coeff()` or Maxima function are not dependent on the dataset. It only takes `n = f / (2 - f)` and the coefficient array as parameter.   
  
Please let me know if I should provide any further details.

---

## Comment 9

> Username: vissarion  
> Created_at: 2018-06-06 08:36:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-394989159  

@adl1995  thanks for the details.  
  
Still I do not understand how do you recognize which case is the accurate and which the inaccurate one. I see that you assume that original GeographicLib code gives the accurate result and maybe you are correct but I do not see the reasoning. Is it possible for example that GeographicLib returns an inaccurate result using `Geodesic::C3coeff()` and the use of the Maxima function yields the accurate one?

---

## Comment 10

> Username: adl1995  
> Created_at: 2018-06-06 11:47:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-395040655  

The reason I am inclined to recognize `Geodesic::C3coeff()` function as accurate is that it produces results that match with dataset/Boost.Geometry.  
  
However, I agree that this alone should not be the basis of identifying the inaccuracy. I have looked through the paper [Algorithms for geodesics](https://arxiv.org/pdf/1109.4448.pdf) in which Eq. (25) seems to describe the C3 series expansion. Also, Section 5 and Eq. (53) in the technical report [Geodesics on an ellipsoid of revolution](https://arxiv.org/pdf/1102.1215) provide a more detailed explanation.  
  
With that said, I am not sure how I should go on about debugging this. I am not much familiar with integrals, so confirming if the series expansions are indeed correct might be difficult.  
  
I did notice that the function [evaluate_coeffs_C3_I3](https://github.com/vissarion/geometry/blob/39813fa732e75116b5498ef4f8ca33e6e585087c/include/boost/geometry/formulas/series_expansion_direct.hpp#L330) in the prototype implementation you provided differs from the one I generated through Maxima ([code_C3.txt](https://github.com/boostorg/geometry/files/2075956/code_C3.txt)). Did you use the same [geod.mac](https://geographiclib.sourceforge.io/html/geod.mac) script for generating this?

---

## Comment 11

> Username: vissarion  
> Created_at: 2018-06-06 12:15:51 UTC  
> Updated_at: 2018-06-07 08:03:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-395047654  

I took them from the papers you mentioned, if I remember correctly. The look the same with `Geodesic::C3coeff()`.   
  
For what order did you get the inaccuracy issue? What happens with other orders?

---

## Comment 12

> Username: adl1995  
> Created_at: 2018-06-06 15:29:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-395111056  

I just tried modifying the series order. The tests for `lon2` pass when order is set to `2`, but fail at all other values i.e. `3`, `4`, `5`, `6`, `7`, `8`.

---

## Comment 13

> Username: vissarion  
> Created_at: 2018-06-07 08:19:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-395334634  

> I just tried modifying the series order. The tests for lon2 pass when order is set to 2, but fail at all other values i.e. 3, 4, 5, 6, 7, 8.  
  
ok this sheds some light. It seems that the Boost.Geometry dataset is created with order 2 series so you can use that order to your tests too.

---

## Comment 14

> Username: adl1995  
> Created_at: 2018-06-09 11:49:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-395963032  

@vissarion Thanks for the suggestion!  
  
I have now replaced `Geodesic::C3coeff()` with Maxima generated function. All tests are passing with series order 2 with dataset/Boost.Geometry.  
  
For dataset/GeographicLib, however, the tests still fail for geodesic scale (M12), which was calculated manually since it is absent from GeodTest.dat. Previously, this was calculated for series order 6. I have now updated it with series order 7. This reduces the number of failing test cases to 87 (previously 99).  
  
Quoting from [GeodTest Zenodo page](https://zenodo.org/record/32156):  
  
> These [test set values] are computed using high-precision direct geodesic calculations.  
  
The instructions for [building GeographicLib using high-precision arithmetic](https://geographiclib.sourceforge.io/html/highprec.html) state that it requires the quadmath library. But, I'm unable to find it using `apt`. @vissarion, have you tried building the library this way before? I'm not sure it this will resolve the above issue with M12, but it might be worth a try.

---

## Comment 15

> Username: vissarion  
> Created_at: 2018-06-11 09:38:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-396183695  

>The instructions for building GeographicLib using high-precision arithmetic state that it requires the quadmath library. But, I'm unable to find it using apt. @vissarion, have you tried building the library this way before? I'm not sure it this will resolve the above issue with M12, but it might be worth a try.  
  
There is a `libquadmath0` debian package. I haven't build GeographicLib with high precision arithmetic, but I agree that it worth a try. Before that what about testing with series order 8---instead of 7.

---

## Comment 16

> Username: adl1995  
> Created_at: 2018-06-12 07:18:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-396490770  

> There is a libquadmath0 debian package. I haven't build GeographicLib with high precision arithmetic, but I agree that it worth a try. Before that what about testing with series order 8---instead of 7.  
  
I already tried testing with series order 8, and all lower values, but I get the same results.  
  
So, I just calculated [geodesic scale (M12) using high precision arithmetic](https://github.com/boostorg/geometry/pull/486/commits/2cba2fa83fe62c52efd5fc2d7fcb1401264701cf). I only had to change the compiler to `gcc-7`, which comes pre-packaged with `libquadmath`. However, that did not resolve the issue, as the tests are still failing. It seems the issue must be with the library implementation. I'll try debugging it again.  
  
If you have any suggestions, please let me know.

---

## Comment 17

> Username: adl1995  
> Created_at: 2018-06-13 07:31:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-396842246  

@vissarion The issue above is now fixed! It turned out to be a minor calculation mistake which led to the inaccuracy in M12. I had multiplied `(sin_sigma2 * sin_sigma1)` instead of adding them `(sin_sigma2 + sin_sigma1)`.  
  
All tests are passing now. Please let me know if this pull request requires further changes.

---

## Review 18 [Commented]

> Username: vissarion  
> Created_at: 2018-06-14 08:50:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-128693976  

📁 include/boost/geometry/util/math.hpp

```diff
 773 | 
 774 |+ /*!
 775 |+ \brief Evaluate the sine and cosine function with the argument in degrees
```

> Username: vissarion  
> Created_at: 2018-06-14 08:50:47 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195345467  

why is it needed to compute the trigonometric functions with the argument in degrees and not in radians? The input of direct formulas are in radians

> Username: adl1995  
> Created_at: 2018-06-15 07:32:50 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195652989  

Actually, the input to Karney direct is in degrees. This can be seen in [direct.cpp](https://github.com/BoostGSoC18/geometry/blob/feature/geodesic_direct/test/formulas/direct.cpp#L44) test file.

> Username: vissarion  
> Created_at: 2018-06-15 07:41:49 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195654694  

I see, it is desirable to have a common interface for formulas with same functionality (all apart from Karney's direct accept radians) but I guess it is not straightforward to convert Karney's code to accept radians, is it? Any intuition for why degrees instead of radians are used? Are the computations simpler, faster or more accurate?

> Username: adl1995  
> Created_at: 2018-06-18 13:14:52 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r196072219  

Currently, the internal functions assume input in degrees. It might be possible to make the switch to radians. However, wouldn't it be simpler if the user could provide input in radians and we convert it into degrees at the beginning? Or, would this be inefficient?  
  
Neither the paper nor GeographicLib documentation go into detail on why this design was chosen. I don't think the computations are any faster because of this, because the angles are manually converted into radians at various points in the code.

> Username: vissarion  
> Created_at: 2018-06-20 07:13:50 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r196669160  

>Currently, the internal functions assume input in degrees. It might be possible to make the switch to radians. However, wouldn't it be simpler if the user could provide input in radians and we convert it into degrees at the beginning? Or, would this be inefficient?  
  
I am not sure that I understand what you want to say. The user can use either radians or degrees and this is converted to radians and passed to formulas for computations.  
  
>Neither the paper nor GeographicLib documentation go into detail on why this design was chosen. I don't think the computations are any faster because of this, because the angles are manually converted into radians at various points in the code.  
  
If there are a lot of conversions it should be investigated whether this can be avoided by replacing the current function by one that uses only radians. Maybe in a separate PR.

> Username: adl1995  
> Created_at: 2018-06-20 11:46:26 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r196743701  

@vissarion So, I will perform the conversion to radians in a separate PR. I have created an [issue](https://github.com/BoostGSoC18/geometry/issues/2) as a reminder for myself.  
  
If there are no further changes required with this PR, can we please merge this?

> Username: awulkiew  
> Created_at: 2018-06-21 15:27:41 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197177486  

It's possible that the reason is that it's not possible to express PI or PI/2 but it is possible to express 180 or 90. But I'm not sure if this is a problem in practice.


---

## Review 19 [Commented]

> Username: vissarion  
> Created_at: 2018-06-14 08:53:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-128694885  

📁 include/boost/geometry/util/math.hpp

```diff
 832 |+ /*!
 833 |+ \brief Normalize the given values.
 834 |+ */
```

> Username: vissarion  
> Created_at: 2018-06-14 08:53:06 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195346186  

this is used in other places of the library. Could you add a todo here as for horner evaluation?

> Username: vissarion  
> Created_at: 2018-06-14 09:50:19 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195363012  

Also, maybe the right place to put normalization functions is util/normalize_spheroidal_coordinates.hpp and try not to duplicate functionality with the already implemented functions there.

> Username: adl1995  
> Created_at: 2018-06-15 09:50:26 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195684758  

I believe [normalize_angle](https://github.com/BoostGSoC18/geometry/blob/feature/geodesic_direct/include/boost/geometry/util/math.hpp#L848) and [normalize_longitude](https://github.com/BoostGSoC18/geometry/blob/feature/geodesic_direct/include/boost/geometry/util/normalize_spheroidal_coordinates.hpp#L358) serve the same purpose. However, using the latter for normalizing, say azimuth, would be semantically incorrect. In this case, should I keep the `normalize_angle` function?

> Username: vissarion  
> Created_at: 2018-06-15 09:55:55 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195685979  

If they are the same you can try to create a general normalize_angle in util/normalize_spheroidal_coordinates.hpp and then call it from  normalize_longitude as well as your code.

> Username: adl1995  
> Created_at: 2018-06-15 13:23:42 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195730597  

I have moved the `normalize_angle` function to [util/normalize_spheroidal_coordinates.hpp#L375](https://github.com/BoostGSoC18/geometry/blob/feature/geodesic_direct/include/boost/geometry/util/normalize_spheroidal_coordinates.hpp#L375), where it calls `normalize_longitude` under the hood.  
  
Also, I noticed that `normalize` from `math.hpp` is already present in [formulas/vertex_longitude.hpp#L59](https://github.com/BoostGSoC18/geometry/blob/feature/geodesic_direct/include/boost/geometry/formulas/vertex_longitude.hpp#L59). I will merge these in a separate PR. For now, this has also been moved to `util/normalize_spheroidal_coordinates.hpp`.


---

## Review 20 [Commented]

> Username: vissarion  
> Created_at: 2018-06-14 08:58:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-128696836  

📁 include/boost/geometry/util/math.hpp

```diff
 846 |+ */
 847 |+ template<typename T>
 848 |+     inline T normalize_angle(T x)
```

> Username: vissarion  
> Created_at: 2018-06-14 08:58:19 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195347657  

tab


---

## Review 21 [Commented]

> Username: vissarion  
> Created_at: 2018-06-14 08:58:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-128696976  

📁 include/boost/geometry/util/math.hpp

```diff
 878 |+ template<typename CT>
 879 |+ inline CT polyval(int N,
 880 |+                          const CT coeff[],
```

> Username: vissarion  
> Created_at: 2018-06-14 08:58:40 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195347777  

tab


---

## Review 22 [Commented]

> Username: vissarion  
> Created_at: 2018-06-14 09:34:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-128709600  

📁 doc/other/maxima/geod.mac

```diff
   1 |+ /*
```

> Username: vissarion  
> Created_at: 2018-06-14 09:34:11 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195358093  

copyright info

> Username: vissarion  
> Created_at: 2018-06-14 09:37:27 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195359015  

similarly add your name to the contributors in the begging of every file that you change

> Username: adl1995  
> Created_at: 2018-06-15 09:45:12 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195683451  

@vissarion Regarding the copyright, one whose behalf should I add this? And since `geod.mac` file is taken from GeographicLib, shouldn't it contain its respective copyright?

> Username: vissarion  
> Created_at: 2018-06-15 09:51:47 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195685058  

>Regarding the copyright, one whose behalf should I add this?  
  
Just yourself. You can acknowledge gsoc.   
  
>And since geod.mac file is taken from GeographicLib, shouldn't it contain its respective copyright?  
  
You can add something similar to this:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/srs/projections/proj/aea.hpp

> Username: adl1995  
> Created_at: 2018-06-15 16:45:59 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195801024  

I've just added the [copyright information and updated the contributors list](https://github.com/boostorg/geometry/pull/486/commits/82c5c4d20c8f51dbd9d9e788b824ad06675a897e). Please let me know if it requires changes.

> Username: vissarion  
> Created_at: 2018-06-18 07:13:54 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r195981083  

"on behalf of" sounds a bit unclear. What about, "as part of"? Or "partially supported by gsoc18" in a separate sentence?

> Username: adl1995  
> Created_at: 2018-06-18 13:06:07 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r196069537  

I have replaced it with "... as part of Google Summer of Code 2018 program".


---

## Comment 23

> Username: vissarion  
> Created_at: 2018-06-20 18:44:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-398855621  

@awulkiew do you have any comments on this PR?

---

## Review 24 [Changes requested]

> Username: awulkiew  
> Created_at: 2018-06-21 16:20:31 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-130825960  

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 112 |+         math::normalize_values<CT>(sin_beta1, cos_beta1);
 113 |+ 
 114 |+         cos_beta1 = std::max(sqrt(std::numeric_limits<CT>::min()), cos_beta1);
```

> Username: awulkiew  
> Created_at: 2018-06-21 13:53:56 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197141278  

For user-defined numeric types `numeric_limits<>` is not specialized. Why not simply have CT(0) here instead? Is something positive really needed?

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 122 |+         CT const k2 = math::sqr(cos_alpha0) * ep2;
 123 |+ 
 124 |+         CT const epsilon = k2 / (c2 * (c1 + sqrt(c1 + k2)) + k2);
```

> Username: awulkiew  
> Created_at: 2018-06-21 13:59:52 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197143475  

Use `math::sqrt()`.

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 131 |+         // Index zero element of coeffs_C1 is unused.
 132 |+         CT coeffs_C1[SeriesOrder + 1];
 133 |+         series_expansion::evaluate_coeffs_C1<CT, SeriesOrder>(epsilon, coeffs_C1);
```

> Username: awulkiew  
> Created_at: 2018-06-21 14:30:55 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197155370  

Ok, so in `evaluate_coeffs_C1()` there is an assumption that the size of array `coeffs_C1` is consistent with `SeriesOrder`. I suggest to pass an object of a type containing the info about the compile-time size. So either implement your own compile-time size container of coefficients or e.g. use `boost::array<>`. The information about CT will also be a part of type so you won't be forced to pass neither of the template parameters.

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 158 |+         // Index zero element of coeffs_C1p is unused.
 159 |+         CT coeffs_C1p[SeriesOrder + 1];
 160 |+         series_expansion::evaluate_coeffs_C1p<CT, SeriesOrder>(epsilon, coeffs_C1p);
```

> Username: awulkiew  
> Created_at: 2018-06-21 14:38:12 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197158014  

Same here, e.g.:  
  
    se::coeffs<CT, SeriesOrder> coeffs_C1p;  
    se::evaluate_coeffs_C1p(epsilon, coeffs_C1p);      
  
or  
  
    boost::array<CT, SeriesOrder + 1> coeffs_C1p;  
    se::evaluate_coeffs_C1p(epsilon, coeffs_C1p);  
  
Btw, is `coeffs_C1p` calculated from `epsilon` or rather generated for specific `SeriesOrder` and `epsilon` is only a parameter? If the latter is true, i.e. `epsilon` only a parameter then I think the order of arguments could be reversed.

> Username: adl1995  
> Created_at: 2018-06-22 14:35:27 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197465437  

@awulkiew Yes, epsilon is only a parameter.  
  
Reiterating what you said above, in case I use `boost::array`, I won't have to explicitly provide the template parameters, but they will still be part of the function header, right?  
  
This works fine for most cases, however, in some cases the array size is defined as below:  
```cpp  
size_t const coeffs_C3_size = (SeriesOrder * (SeriesOrder - 1)) / 2;  
```  
The array can be defined as:  
```cpp  
boost::array<CT, coeffs_C3_size> coeffs_C3x;  
```  
Which is then passed to this function:  
```cpp  
template <typename CT, size_t SeriesOrder>  
static inline void evaluate_coeffs_C3x(CT const& n, boost::array<CT, SeriesOrder>& c)  
{ ... }  
```  
In this case, the `SeriesOrder` template parameter will be the updated array size, hence the `switch` statement will not have any effect, since the value is out of bound.  
  
One solution is to pass an additional argument to the function, which represents `SeriesOrder`. Another way to solve this is by converting the series expansions functions to class methods, and storing `SeriesOrder` as a class member. Please let me know if a there's a better alternative.  
  
I have [this commit](https://github.com/BoostGSoC18/geometry/commit/1972bcda3e5c204f2a0d0bda66239725828f1306), which might make it easier to follow what I am trying to say above.

> Username: awulkiew  
> Created_at: 2018-06-22 16:24:22 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197498707  

@adl1995 Yes the most simple solution is to pass `SeriesOrder` as separate parameter:  
  
    template <size_T SeriesOrder, typename CT, size_t N>  
    inline void evaluate_coeffs_C3x(boost::array<CT, N>& c, CT const& n)  
    {  
        BOOST_MPL_ASSERT(N == (SeriesOrder * (SeriesOrder - 1)) / 2);  
        // ...  
    }  
  
but then the caller of this function has to know what's the required size of array for passed `SeriesOrder`. It would be better to have it all in one place. Would it have sense to implement several representations of coefficient containers? So in this case something like:  
  
    template <typename T, size_t SeriesOrder>  
    struct coeffs_C1 : boost::array<T, SeriesOrder + 1>  
    {  
        coeffs_C1(T const& epsilon)  
        {  
            // the content of evaluate_coeffs_C1()  
        }  
    };  
  
    template <typename T, size_t SeriesOrder>  
    struct coeffs_C3x : boost::array<T, (SeriesOrder * (SeriesOrder - 1)) / 2)>  
    {  
        coeffs_C3x(T const& n)  
        {  
            // the content of evaluate_coeffs_C3x()  
        }  
    };  
  
    // etc.  
  
this way the caller would only have to know the `SeriesOrder`. This would be the same for all coefficients. He wouldn't be forced to know how to calculate the size of array, if he should add 1 to size or not, etc. Everything would be defined in one place.  
  
And then `sin_cos_series()` which AFAIU always calculates the series using all elements of array would take the size from `static_size` member (`Coeffs` is derived from `boost::array<>`):  
  
    template <typename CT, typename Coeffs>  
    inline CT sin_cos_series(CT const& sinx, CT const& cosx, Coeffs const& coeffs)  
    {  
        // and then:  
        size_t const size = Coeffs::static_size;  
        CT el0 = coeffs[0];  
    }  
  
Does it have sense or is there something about coeffiients that I don't understand?

> Username: adl1995  
> Created_at: 2018-06-22 16:59:41 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197507829  

@awulkiew Thanks. Yes, this makes perfect sense. This will also make the code in `karney_direct.hpp` simpler. Also, `series_expansion.hpp` seems like a better place to include size computation logic for coefficient containers.

> Username: adl1995  
> Created_at: 2018-06-25 12:26:20 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197776667  

@awulkiew I have addressed these changes in [this commit](https://github.com/BoostGSoC18/geometry/commit/dedccdbdaeaddc2fb0d27568bbe2dc00b74da316). Please let me know if this can be further improved somehow.  
  
For `coeffs_C3x` and `coeffs_C3`, I have created a single wrapper `struct`, as `coeffs_C3x` is not directly used within the code. Also, I have replaced the last `case` of each `switch` statement with a `default`.

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 161 |+ 
 162 |+         CT const B12 =
 163 |+             - series_expansion::sin_cos_series<CT, SeriesOrder>
```

> Username: awulkiew  
> Created_at: 2018-06-21 14:38:50 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197158283  

The above changes would also allow you to get rid of these template arguments, e.g.:  
  
    CT const B12 = - se::sin_cos_series(sin_tau1 * cos_tau12 + cos_tau1 * sin_tau12,  
                                        cos_tau1 * cos_tau12 - sin_tau1 * sin_tau12,  
                                        coeffs_C1p);

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 127 |+         // series expansion using Horner scehme.
 128 |+         CT const expansion_A1
 129 |+             = series_expansion::evaluate_series_A1<CT, SeriesOrder>(epsilon);
```

> Username: awulkiew  
> Created_at: 2018-06-21 14:44:33 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197160681  

To shorten this line you could:  
- add namespace alias in this function scope (here or at the begginning),  
- reverse the order of template parameters allowing CT to be deduced by the compiler.  
  
Regarding the name, can `A1` be something else than `series_A1`? In other words, since this function is in namespace `series_expansion`, isn't the word `series` in `evaluate_series_A1` redundant? Could it simply be `evaluate_A1`?  
  
So it'd become:  
  
    namespace se = series_expansion;  
    CT const expansion_A1 = se::evaluate_A1<SeriesOrder>(epsilon);

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 173 |+             = sin_sigma1 * cos_sigma12 + cos_sigma1 * sin_sigma12;
 174 |+         CT const cos_sigma2
 175 |+             = cos_sigma1 * cos_sigma12 - sin_sigma1 * sin_sigma12;
```

> Username: awulkiew  
> Created_at: 2018-06-21 14:48:55 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197162469  

Why not have it all in one line? Is it too long? If this is the case then be consistent with the assignment operator. Should it be at the end of the first line or at the beginning of the next?

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 183 |+ 
 184 |+             // Convert the angle to radians.
 185 |+             result.reverse_azimuth /= math::d2r<T>();
```

> Username: awulkiew  
> Created_at: 2018-06-21 14:51:12 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197163359  

Shouldn't it be `math::d2r<CT>()`?

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 197 |+ 
 198 |+             // Convert the coordinate to radians.
 199 |+             result.lat2 /= math::d2r<T>();
```

> Username: awulkiew  
> Created_at: 2018-06-21 14:51:21 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197163436  

Shouldn't it be `math::d2r<CT>()`?

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 207 |+ 
 208 |+             CT coeffs_A3[SeriesOrder];
 209 |+             series_expansion::evaluate_coeffs_A3<CT, SeriesOrder>(n, coeffs_A3);
```

> Username: awulkiew  
> Created_at: 2018-06-21 14:51:38 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197163563  

Same as the above.

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 213 |+ 
 214 |+             // Compute the size of coefficient array.
 215 |+             size_t const coeffs_C3_size = (SeriesOrder * (SeriesOrder - 1)) / 2;
```

> Username: awulkiew  
> Created_at: 2018-06-21 14:54:24 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197164740  

This should be compile time-constant.

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 214 |+             // Compute the size of coefficient array.
 215 |+             size_t const coeffs_C3_size = (SeriesOrder * (SeriesOrder - 1)) / 2;
 216 |+             CT coeffs_C3x[coeffs_C3_size];
```

> Username: awulkiew  
> Created_at: 2018-06-21 14:55:32 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197165197  

Would it have sense to have another representation of coefficients with size calculated inside? E.g. `se::coeffs_XX<CT, SeriesOrder>` with `XX` being something to distinguish it from the previous representation? Though below you have `CT coeffs_C3[SeriesOrder]` with yet another size.

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 219 |+             // Evaluate C3 coefficients.
 220 |+             CT coeffs_C3[SeriesOrder];
 221 |+             series_expansion::evaluate_coeffs_C3<CT, SeriesOrder>(epsilon, coeffs_C3, coeffs_C3x);
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:13:38 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197172009  

The input and output arguments should be consistent across functions.  
For example, you could:  
- mimic STL algorithms or Boost.Geometry algorithms. First the input container, then output container, then parameters. so:  
    `se::evaluate_coeffs_C3(coeffs_C3x, coeffs_C3, epsilon);`  
  
- always pass the output object as the first one:  
    `se::evaluate_coeffs_C3(coeffs_C3, coeffs_C3x, epsilon);`  
  
However after reviewing greater portion of the code I propose to implement these coefficients as class templates and whatever is done in `evaluate_XXX` do it in the constructor. In this case the code above would look like this:  
  
    se::coeffs_C3x<CT, SeriesOrder> coeffs_C3x(n);  
    se::coeffs_C3<CT, SeriesOrder> coeffs_C3(coeffs_C3x, epsilon);  
  
Would it have sense or because coefficients may be filled in so many ways would it make more sense to separate data representation and algorithm as it is now?

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 228 |+                                                            (sin_sigma2,
 229 |+                                                             cos_sigma2,
 230 |+                                                             coeffs_C3) - B31));
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:21:56 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197175283  

Is everything ok here? Above, the size of `coeffs_C1` was `SeriesOrder + 1` and here the size of `coeffs_C3` is `SeriesOrder`. AFAIU these are plain arrays so `sin_cos_series()` cannot be overloaded. What am I missing? Is the last element of `coeffs_C1` ignored in `sin_cos_series()`? But if it is ignored here then why is it stored in the array and not simply a local variable in a function filling this array?

> Username: adl1995  
> Created_at: 2018-06-26 05:39:59 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198018742  

I think the `sin_cos_series` function is fixed now. Previously,  
I was passing `SeriesOrder` for all coefficients, but now the  
size is queried from `boost::array`'s `static_size` member.  
  
So, if the container size is `9`, `n` is set to `(9 - 1)`. This line:  
```  
CT k0 = n & 1 ? coeffs[--index] : 0;  
```  
checks if `n` is odd (not in this case), so `k0` is set to `0`.  
  
The loop runs 4 times because of `n /= 2`.  
So, in total 8 elements are accessed (excluding the first one).  
  
If the container size is `8`, then all the container elements are  
used for computing the result.

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 232 |+             // Convert to radians to get the
 233 |+             // longitudinal difference.
 234 |+             CT lon12 = lam12 / math::d2r<T>();
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:22:06 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197175338  

`CT`

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 247 |+             // Evaluate the coefficients for C2.
 248 |+             // Index zero element of coeffs_C2 is unused.
 249 |+             CT coeffs_C2[SeriesOrder + 1];
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:22:53 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197175633  

Here too.

---

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 264 |+             CT const J12 = (expansion_A1 - expansion_A2) * sigma12 + (AB1 - AB2);
 265 |+ 
 266 |+             CT const dn1 = sqrt(c1 + ep2 * math::sqr(sin_beta1));
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:24:45 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197176395  

`math::sqrt()`


📁 include/boost/geometry/util/math.hpp

```diff
 858 |+ template<typename CT>
 859 |+ inline CT polyval(int N,
 860 |+                   const CT coeff[],
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:33:52 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197179720  

Avoid passing array and size separately. Pass Range and get its size using `boost::size()` or if the size is compile-time pass e.g. `boost::array<>` and get its compile-time as `coeff.static_size`.  
  
Also if the latter is true, i.e. it's compile-time size then consider reimplementing this function using metaprogramming to avoid run-time loop. Thought this is probably not required because in practice the compiler should probably optimize the loop.

> Username: adl1995  
> Created_at: 2018-06-26 08:29:03 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198055501  

The array size changes with each iteration in `evaluate_coeffs_C3` function, so it is not compile-time constant.  
  
I've just [updated](https://github.com/BoostGSoC18/geometry/commit/9c96bec2bf8521e720a97674b3b93c626769e702) this function to take `std::vector` as the parameter, and obtained its size using `boost::size(coeff)`.


📁 include/boost/geometry/util/normalize_spheroidal_coordinates.hpp

```diff
 374 |+ */
 375 |+ template <typename Units, typename CoordinateType>
 376 |+ inline void normalize_angle(CoordinateType& angle)
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:35:55 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197180429  

I'm not sure if this is a good name for this function. Clearly not every angle should be normalized with it, e.g. latitude.

> Username: adl1995  
> Created_at: 2018-06-26 12:00:03 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198115712  

Would `normalize_coordinate` or `normalize_1d_coordinate` be a better name for this?

> Username: awulkiew  
> Created_at: 2018-06-26 22:31:23 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198317851  

AFAIU you're using it to normalize longitude and azimuth so you could use `normalize_longitude()` for the former and call this one `normalize_azimuth()` and use it for the latter.

---

📁 include/boost/geometry/util/normalize_spheroidal_coordinates.hpp

```diff
 388 |+ */
 389 |+ template<typename ValueType>
 390 |+ inline void normalize_values(ValueType& x, ValueType& y)
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:37:22 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197180932  

This function has name too general to be here. It's normalization of coordinates of 2d vector.

> Username: adl1995  
> Created_at: 2018-06-26 12:00:24 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198115794  

Should I rename this as `normalize_2d_coordinates`?

> Username: awulkiew  
> Created_at: 2018-06-26 22:34:26 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198318420  

You use it to normalize unit vector so maybe `normalize_unit_vector()` or something along the lines. Something which in the name has a part indicating that the input has to have non-zero length.

---

📁 include/boost/geometry/util/normalize_spheroidal_coordinates.hpp

```diff
 392 |+     ValueType h = boost::math::hypot(x, y);
 393 |+ 
 394 |+     x /= h; y /= h;
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:37:51 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197181102  

What if `h == 0`?

> Username: adl1995  
> Created_at: 2018-06-26 12:09:25 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198118101  

I have added the assertion:  
```  
BOOST_GEOMETRY_ASSERT(h > 0);  
```

> Username: awulkiew  
> Created_at: 2018-06-26 22:36:55 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198318854  

If you're sure that the input should have length greater than `0` then assertion is fine.


📁 include/boost/geometry/util/series_expansion.hpp

```diff
  59 |+         CT eps2 = math::sqr(eps);
  60 |+         CT t;
  61 |+         switch (SeriesOrder/2) {
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:41:35 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197182606  

What if `SeriesOrder/2` is greater than 4? There is no `default:` clause in `switch`.

---

📁 include/boost/geometry/util/series_expansion.hpp

```diff
 104 |+         CT const eps2 = math::sqr(eps);
 105 |+         CT t;
 106 |+         switch (SeriesOrder/2) {
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:44:46 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197183753  

No `default:` clause.

---

📁 include/boost/geometry/util/series_expansion.hpp

```diff
 148 |+     static inline void evaluate_coeffs_A3(CT const& n, CT c[])
 149 |+     {
 150 |+         switch (SeriesOrder) {
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:45:11 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197183906  

`default:`

---

📁 include/boost/geometry/util/series_expansion.hpp

```diff
 219 |+         CT eps2 = math::sqr(eps);
 220 |+         CT d = eps;
 221 |+         switch (SeriesOrder) {
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:45:31 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197184027  

`default:`

---

📁 include/boost/geometry/util/series_expansion.hpp

```diff
 146 |+     // TODO: this produces different results that geographiclib
 147 |+     template <typename CT, std::size_t SeriesOrder>
 148 |+     static inline void evaluate_coeffs_A3(CT const& n, CT c[])
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:51:26 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197186190  

As mentioned in the comments for another file. The array and it's size should not be separately passed into the function. Also `n` name is confusing in this context because it begs to treat it as the size of array. As I mentioned before the most simple thing to do would be to implement it as:  
  
    template <typename CT, std::size_t SeriesOrder>  
    inline void evaluate_coeffs_A3(boost::array<CT, SeriesOrder> & c, CT const& n)

---

📁 include/boost/geometry/util/series_expansion.hpp

```diff
  55 |+     */
  56 |+     template <typename CT, std::size_t SeriesOrder>
  57 |+     static inline CT evaluate_series_A1(CT eps)
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:52:11 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197186456  

All of functions in this file should be `inline`, not `static inline`.

---

📁 include/boost/geometry/util/series_expansion.hpp

```diff
 512 |+     */
 513 |+     template <typename CT, int SeriesOrder>
 514 |+     static inline void evaluate_coeffs_C3x(CT const& n, CT c[]) {
```

> Username: awulkiew  
> Created_at: 2018-06-21 15:57:27 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197188255  

So here the `SeriesOrder` could be passed separately as the first argument (with MPL_ASSERT checking if the size of array is correct), be extracted from your representation of `coeffs_C3x` or this function would be a constructor of a class `coeffs_C3x`.

> Username: adl1995  
> Created_at: 2018-06-26 11:51:40 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198113652  

I have changed the function header as:  
```cpp  
template <typename Coeffs, typename CT>  
static inline void evaluate_coeffs_C3x(Coeffs &c, size_t const& SeriesOrder, CT const& n) {  
```  
I'm currently using:  
```cpp  
BOOST_GEOMETRY_ASSERT(coeff_size == (SeriesOrder * (SeriesOrder - 1)) / 2);  
```  
which works fine. However, if I use:  
```cpp  
BOOST_MPL_ASSERT(coeff_size == (SeriesOrder * (SeriesOrder - 1)) / 2);  
```  
I get this error:  
```cpp  
./boost/geometry/util/series_expansion.hpp: In function ‘void boost::geometry::series_expansion::evaluate_coeffs_C3x(Coeffs&, const size_t&, const CT&)’:  
./boost/mpl/assert.hpp:60:51: error: expected primary-expression before ‘enum’  
 #   define BOOST_MPL_AUX_ASSERT_CONSTANT(T, expr) enum { expr }  
                                                   ^  
./boost/mpl/assert.hpp:285:1: note: in expansion of macro ‘BOOST_MPL_AUX_ASSERT_CONSTANT’  
 BOOST_MPL_AUX_ASSERT_CONSTANT( \  
 ^  
./boost/geometry/util/series_expansion.hpp:576:9: note: in expansion of macro ‘BOOST_MPL_ASSERT’  
BOOST_MPL_ASSERT(coeff_size == (SeriesOrder * (SeriesOrder - 1)) / 2);  
         ^  
```  
  
Is this because both values have to be compile-time constant?

---

📁 include/boost/geometry/util/series_expansion.hpp

```diff
 627 |+     */
 628 |+     template <typename CT, size_t SeriesOrder>
 629 |+     static inline void evaluate_coeffs_C3(CT eps, CT coeffs1[], CT coeffs2[])
```

> Username: awulkiew  
> Created_at: 2018-06-21 16:00:23 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197189249  

`CT const& eps` - remember that `CT` may be user-defined non-fundamental type.  
  
The coeffs parameters are confusing. Their sizes should be part of types. It is not clear which is input and which output.

---

📁 include/boost/geometry/util/series_expansion.hpp

```diff
 632 |+         int offset = 0;
 633 |+ 
 634 |+         // l is the index of C3[l].
```

> Username: awulkiew  
> Created_at: 2018-06-21 16:02:58 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197190034  

If this was separate type and the code was describing itself you wouldn't need to write this comment.

---

📁 include/boost/geometry/util/series_expansion.hpp

```diff
 638 |+             int m = SeriesOrder - l - 1;
 639 |+             mult *= eps;
 640 |+             coeffs1[l] = mult * math::polyval(m, coeffs2 + offset, eps);
```

> Username: awulkiew  
> Created_at: 2018-06-21 16:14:56 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197193785  

The iteration of `l` starts from `1`. Why does the first element is not set?  
  
Right, so `polyval()` takes a subrange of `coeffs2`. The order of parameters is nonintuitive. Typically length of array is passed after array's name. But I'd encourage you to use STL algorithm kind of interface, and take begin and end iterator of subrange.  
  
    math::polyval(coeffs2 + offset, coeffs2 + offset + m, eps);  
  
But this is not optimal, an improvement would be to move the iterator/pointer instead of keeping offset separately, so:  
  
    math::polyval(coeffs2, coeffs2+m, eps);  
    coeffs2 += m + 1;  
  
One variable less. However with `boost::array<>` or other class you'd still have to create separate iterator:  
  
    const_iterator it_C3x = coeffs2.begin()  
    //...  
    coeffs1[l] = mult * math::polyval(it_C3x, it_C3x + m, eps);  
    it_C3x += m + 1;

---

📁 include/boost/geometry/util/series_expansion.hpp

```diff
 657 |+ 
 658 |+         // Point to one beyond last element.
 659 |+         coeffs += (n + 1);
```

> Username: awulkiew  
> Created_at: 2018-06-21 16:17:04 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197194490  

Right, so in `karney_direct.hpp` line 220 there is an error in size of coeffs_C3.  
  
If compile-time of coeffs was part of coeffs' type and you had static assertion here the compiler would inform you about it.

---

📁 include/boost/geometry/util/series_expansion.hpp

```diff
 660 |+         CT ar = 2 * (cosx - sinx) * (cosx + sinx);
 661 |+ 
 662 |+         CT k0 = n & 1 ? *--coeffs : 0;
```

> Username: awulkiew  
> Created_at: 2018-06-21 16:20:16 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r197195512  

Is this test for odd value?

> Username: adl1995  
> Created_at: 2018-06-26 16:13:33 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198208041  

Yes, `k0` is set to `*--coeffs` if `n` is odd.  
  
I have added a comment in the file.


---

## Comment 25

> Username: awulkiew  
> Created_at: 2018-06-21 16:24:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-399163315  

Oops, it seems you have to merge develop into your branch and resolve possible conflicts.  
  
I found some issues. In case you didn't have time to fix all of them do some prioritization. I think fixing them is not required to finish GSoC with positive result but I do think fixing them is required to merge the code to develop.

---

## Review 26 [Commented]

> Username: awulkiew  
> Created_at: 2018-06-26 22:39:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-132230593  

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
 115 |+         math::normalize_values<CT>(sin_beta1, cos_beta1);
 116 |+ 
 117 |+         cos_beta1 = std::max(math::sqrt(c0), cos_beta1);
```

> Username: awulkiew  
> Created_at: 2018-06-26 22:39:33 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198319373  

`std::max` won't compile with msvc. Take it in brackets. Furthermore sqrt(0) is 0. So:  
  
    (std::max)(c0, cos_beta1);  
  
or am I missing something?


---

## Review 27 [Commented]

> Username: awulkiew  
> Created_at: 2018-06-26 22:41:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-132230938  

📁 include/boost/geometry/formulas/karney_direct.hpp

```diff
  81 |+ 
  82 |+         Azi azi12 = azimuth12;
  83 |+         math::normalize_angle<degree, Azi>(azi12);
```

> Username: awulkiew  
> Created_at: 2018-06-26 22:41:05 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198319689  

So if you called it `normalize_azimuth()` then it'd be clear that it has to be in range (-180, 180].


---

## Review 28 [Commented]

> Username: awulkiew  
> Created_at: 2018-06-26 22:47:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-132232270  

📁 include/boost/geometry/util/math.hpp

```diff
 796 |+     remainder *= d2r<T>();
 797 |+ 
 798 |+     T s = std::sin(remainder), c = std::cos(remainder);
```

> Username: awulkiew  
> Created_at: 2018-06-26 22:47:37 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198320821  

Before you used math functions without `std::`. If you specify `std::` then the compiler will not use functions from the namespace where a user-defined numeric type is defined. So please remove `std::`. If you like you can bring `std::` math functions into scope:  
  
    using std::floor;  
    using std::sin;  
    using std::cos;  
  
but they are not used like that in the whole library so I'd say this is not needed.


---

## Review 29 [Commented]

> Username: awulkiew  
> Created_at: 2018-06-26 22:48:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-132232350  

📁 include/boost/geometry/util/math.hpp

```diff
 789 |+     T remainder; int quotient;
 790 |+ 
 791 |+     remainder = std::fmod(x, T(360));
```

> Username: awulkiew  
> Created_at: 2018-06-26 22:48:01 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198320900  

Use `math::mod()`


---

## Review 30 [Commented]

> Username: awulkiew  
> Created_at: 2018-06-26 22:48:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-132232402  

📁 include/boost/geometry/util/math.hpp

```diff
 825 |+     }
 826 |+ 
 827 |+     T y = std::abs(x);
```

> Username: awulkiew  
> Created_at: 2018-06-26 22:48:15 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198320938  

`math::abs()`


---

## Review 31 [Commented]

> Username: awulkiew  
> Created_at: 2018-06-26 22:51:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-132233185  

📁 include/boost/geometry/util/series_expansion.hpp

```diff
 574 |+     inline void evaluate_coeffs_C3x(Coeffs &c, size_t const& SeriesOrder, CT const& n) {
 575 |+         size_t const coeff_size = Coeffs::static_size;
 576 |+         BOOST_GEOMETRY_ASSERT(coeff_size == (SeriesOrder * (SeriesOrder - 1)) / 2);
```

> Username: awulkiew  
> Created_at: 2018-06-26 22:51:52 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198321554  

If you took `SeriesOrder` as template parameter you could check this at compile time:  
  
    static size_t const coeff_size = Coeffs::static_size;  
    static size_t const expected_size = (SeriesOrder * (SeriesOrder - 1)) / 2;  
    BOOST_MPL_ASSERT((coeff_size == expected_size));

> Username: adl1995  
> Created_at: 2018-06-27 06:40:45 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198381412  

I'm having some trouble passing `SeriesOrder` as the template parameter.  
  
I changed the `evaluate_coeffs_C3x` function header to:  
```cpp  
template <typename Coeffs, typename CT, size_t SeriesOrder>  
inline void evaluate_coeffs_C3x(Coeffs &c, CT const& n) {  
```  
and called it as:  
```cpp  
evaluate_coeffs_C3x(*this, n);  
```  
but I get this error:  
```  
error: ‘evaluate_coeffs_C3x’ was not declared in this scope  
```  
I also tried changing the order of parameters as:  
```cpp  
template <size_t SeriesOrder, typename CT, typename Coeffs>  
inline void evaluate_coeffs_C3x(CT const& n, Coeffs &c) {  
```  
but calling the function as:  
```cpp  
evaluate_coeffs_C3x(n, *this);  
```  
or:  
```cpp  
evaluate_coeffs_C3x<SeriesOrder, CT>(n, *this);  
```  
doesn't work either.  
  
It produces the error:  
```  
error: expected primary-expression before ‘>’ token  
```  
and:  
```  
couldn't deduce template parameter ‘SeriesOrder’  
```  
respectively.

> Username: awulkiew  
> Created_at: 2018-06-27 13:13:54 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198489542  

If you're calling these functions inside structs' constructors then they should be defined before structs.  
  
Either of these should work:  
  
    template <size_t SeriesOrder, typename CT, typename Coeffs>  
    inline void evaluate_coeffs_C3x(CT const& n, Coeffs &c){}  
  
    // this is more strict, it takes only the struct you like  
    template <size_t SeriesOrder, typename CT>  
    inline void evaluate_coeffs_C3x(CT const& n, coeffs_C3x<SeriesOrder, CT> &c){}  
  
and either of these should work:  
  
    evaluate_coeffs_C3x<SeriesOrder>(n, *this);  
    evaluate_coeffs_C3x<SeriesOrder, CT>(n, *this);  
    evaluate_coeffs_C3x<SeriesOrder, CT, coeffs_C3x>(n, *this); // assuming it's called in coeffs_C3x's constructor

> Username: adl1995  
> Created_at: 2018-06-28 05:03:55 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198708261  

Thanks, this worked.  
  
Out of curiosity, why was this working before for the previous function calls? Does this have something to do with passing explicit template parameters? As I only got the error with `evaluate_coeffs_C3x<SeriesOrder>`.

> Username: awulkiew  
> Created_at: 2018-06-28 13:25:38 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198838532  

No idea. You'd have to know how exactly type deduction is done in your compiler. Are you using Visual Studio? Its compiler allows things it shouldn't sometimes.

> Username: adl1995  
> Created_at: 2018-06-28 13:41:35 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198843956  

No, I'm using `g++` version `7.2.0` on Ubuntu.


---

## Review 32 [Commented]

> Username: awulkiew  
> Created_at: 2018-06-26 22:54:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-132233712  

📁 include/boost/geometry/util/series_expansion.hpp

```diff
 702 |+             mult *= eps;
 703 |+ 
 704 |+             std::vector<CT> coeffs2_slice(coeffs2.begin(), coeffs2.begin() + offset);
```

> Username: awulkiew  
> Created_at: 2018-06-26 22:54:22 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198321979  

You don't have to create a container here, one each iteration btw. This will be horribly slow. Pass range into `math::polyval()`.


---

## Review 33 [Commented]

> Username: awulkiew  
> Created_at: 2018-06-26 22:56:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-132234193  

📁 include/boost/geometry/util/math.hpp

```diff
 857 |+ */
 858 |+ template<typename CT>
 859 |+ inline CT polyval(std::vector<CT> coeff,
```

> Username: awulkiew  
> Created_at: 2018-06-26 22:56:54 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198322402  

template <typename It>  
    inline CT polyval(It first, It last, std::iterator_traits<It>::value_type const& eps)  
  
or just  
  
    template <typename It, typename T>  
    inline CT polyval(It first, It last, T const& eps)


---

## Review 34 [Commented]

> Username: awulkiew  
> Created_at: 2018-06-26 22:57:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-132234286  

📁 include/boost/geometry/util/math.hpp

```diff
 860 |+                   const CT eps)
 861 |+ {
 862 |+     int N = boost::size(coeff) - 1;
```

> Username: awulkiew  
> Created_at: 2018-06-26 22:57:27 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198322477  

`std::distance(first, last) - 1`


---

## Review 35 [Commented]

> Username: awulkiew  
> Created_at: 2018-06-26 22:58:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/486#pullrequestreview-132234440  

📁 include/boost/geometry/util/math.hpp

```diff
 863 |+     int index = 0;
 864 |+ 
 865 |+     CT y = N < 0 ? 0 : coeff[index++];
```

> Username: awulkiew  
> Created_at: 2018-06-26 22:58:16 UTC  
> Updated_at: 2018-06-29 09:54:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#discussion_r198322618  

`*(first + (index++))`


---

## Comment 36

> Username: adl1995  
> Created_at: 2018-06-29 09:57:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-401308899  

Thank you for the detailed reviews. I think all the requested changes have been addressed. I have also merged `develop` into this branch, and resolved all conflicting files.  
  
Please let me know if there are any further changes required.

---

## Comment 37

> Username: awulkiew  
> Created_at: 2018-07-02 13:22:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-401802452  

Thanks! I'm ok with merging. I'd prefer to wait until after the 1.68 release. Or do you need this PR merged in order to work on the next step of the project?

---

## Comment 38

> Username: adl1995  
> Created_at: 2018-07-02 14:51:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-401831889  

That's fine. I can work on local branches for the next part i.e. the inverse method.  
  
Btw, when is the next release scheduled for?

---

## Comment 39

> Username: awulkiew  
> Created_at: 2018-07-03 12:31:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-402139427  

Here is the schedule: https://www.boost.org/development/index.html  
The branches for final release are closed 1 Aug however after beta is released we only do bugfixing.  
  
I think the whole month is too long to keep you wait so I'm ok with merging it now. If needed I'll create a branch for bugfixing based on previous develop state.

---

## Comment 40

> Username: adl1995  
> Created_at: 2018-07-03 14:05:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/486#issuecomment-402169428  

Okay, as you deem fit. If it is merged before August 14th, I can mention this in my final evaluation report.

---
