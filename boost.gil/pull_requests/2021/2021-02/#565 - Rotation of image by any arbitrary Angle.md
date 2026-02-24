# #565 Rotation of image by any arbitrary Angle [Merged]

> Username: gkumar28  
> Created at: 2021-02-27 21:18:24 UTC  
> Updated at: 2021-03-15 19:05:23 UTC  
> Merged at: 2021-03-09 04:56:21 UTC  
> Closed at: 2021-03-09 04:56:21 UTC  
> Url: https://github.com/boostorg/gil/pull/565  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
## [Feature] Added Rotation function to rotate image from its center in numeric/affine.hpp  
### Description  
  
The rotation function already existed however, the rotation was about the top-left vertex of the image as the origin, resulting in some parts being out of view. I have added a function that rotates the image about its center, using affine transformations, and scales it according to the dimensions of the source image.  
  
This pull request is in reference to issue #533   
  
### References  
#533   
  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2021-02-27 21:58:12 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/565#pullrequestreview-600197802  

Please, follow the contributing guidelines

📁 include/boost/gil/extension/numeric/affine.hpp

```diff
 109 | }
 110 | 
 111 |+ //feature
```

> Username: mloskot  
> Created_at: 2021-02-27 21:52:26 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r584195541  

What is this for?

> Username: gkumar28  
> Created_at: 2021-03-01 20:59:24 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r585045116  

I have removed it.

---

📁 include/boost/gil/extension/numeric/affine.hpp

```diff
 111 |+ //feature
 112 |+ //rotate an image from its center point for -180 deg <= theta <= 180 deg
 113 |+ //using consecutive affine transformations
```

> Username: mloskot  
> Created_at: 2021-02-27 21:52:57 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r584195562  

In English, statement starts with capital letter and ends with `.`

---

📁 include/boost/gil/extension/numeric/affine.hpp

```diff
 114 |+ //As top-left corner of source image is taken as origin, idea is to rotate the source image about the origin and then translate the new image
 115 |+ //according to the mentioned rotation angle, and to re-center and fit the image in the source dimensions 
 116 |+ // preferably use type double for less precision errors
```

> Username: mloskot  
> Created_at: 2021-02-27 21:53:26 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r584195596  

- `//<space>`  
- Please, check the guidelines about the line length limit

> Username: gkumar28  
> Created_at: 2021-03-01 21:00:41 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r585045950  

I have rewritten all of the comments accordingly.

> Username: mloskot  
> Created_at: 2021-03-01 21:27:50 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r585061743  

By the way, no need to comment about what you have done, just hit the "Resolve conversation" and the updated diff will show what's been done exactly.  
  
Thanks

---

📁 include/boost/gil/extension/numeric/affine.hpp

```diff
 115 |+ //according to the mentioned rotation angle, and to re-center and fit the image in the source dimensions 
 116 |+ // preferably use type double for less precision errors
 117 |+ template<typename T, typename F> boost::gil::matrix3x2<F> center_rotate(boost::gil::point<T> dims,F rads)
```

> Username: mloskot  
> Created_at: 2021-02-27 21:54:22 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r584195680  

```cpp  
template<typename T, typename F>  
boost::gil::matrix3x2 ...  
```  
  
Doxygen documentation of the function prototype is missing (e.g. what is `F`, etc.)

> Username: gkumar28  
> Created_at: 2021-03-01 21:24:31 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r585059749  

I have added them here  
  
https://github.com/boostorg/gil/pull/565/files#diff-89d5dde7ab09b71c771dca84e09c6c12bcdf7a5466eb3b65beff1882e77ba8f2R111-R118

---

📁 include/boost/gil/extension/numeric/affine.hpp

```diff
 118 |+ {   
 119 |+     while(rads < F(-M_PI)) rads = rads + F(M_PI);
 120 |+     while(rads > F(M_PI)) rads = rads - F(M_PI);
```

> Username: mloskot  
> Created_at: 2021-02-27 21:54:46 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r584195698  

`rads = ...` in new line

> Username: gkumar28  
> Created_at: 2021-03-01 21:40:38 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r585069073  

I have cleaned it up   
  
https://github.com/boostorg/gil/pull/565/files#diff-89d5dde7ab09b71c771dca84e09c6c12bcdf7a5466eb3b65beff1882e77ba8f2R127-R135

---

📁 include/boost/gil/extension/numeric/affine.hpp

```diff
 127 |+     else { translation.c = -s_theta; }
 128 |+ 
 129 |+     if(std::abs(rads) > F(M_PI/2)) { translation.a = -c_theta, translation.d = -c_theta; }
```

> Username: mloskot  
> Created_at: 2021-02-27 21:56:07 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r584195874  

Please, don't try to squeeze everything into a single line!

---

📁 include/boost/gil/extension/numeric/affine.hpp

```diff
 130 |+ 
 131 |+     return boost::gil::matrix3x2<F>::get_scale(s_theta * dims.y / dims.x + c_theta, s_theta * dims.x / dims.y + c_theta) *  //to fit inside given dimensions
 132 |+            boost::gil::matrix3x2<F>::get_translate(dims * translation) *  //re-center the image [top-left of image taken as (0,0)]
```

> Username: mloskot  
> Created_at: 2021-02-27 21:57:32 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r584196041  

Please, respect the line length limit  
Perhaps add some intermediate results, then comments could go into separate lines, instead of squeezing everything into long expression of the `return` statement.

> Username: gkumar28  
> Created_at: 2021-03-01 21:46:44 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r585072488  

I have re-written the code with exceeding length limits


---

## Comment 2

> Username: gkumar28  
> Created_at: 2021-02-28 20:29:47 UTC  
> Url: https://github.com/boostorg/gil/pull/565#issuecomment-787517558  

Sorry for the mess. I'll create a new PR incorporating the changes

---

## Comment 3

> Username: lpranam  
> Created_at: 2021-02-28 20:34:02 UTC  
> Updated_at: 2021-02-28 20:35:48 UTC  
> Url: https://github.com/boostorg/gil/pull/565#issuecomment-787518237  

@gkumar28, please do not keep creating new PRs for the changes. Address all the changes in the same PR. It helps in keeping track of what changes are addressed or what more needs to be done. I think this PR was closed by mistake as I can see.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2021-03-01 01:52:25 UTC  
> Updated_at: 2021-03-03 11:05:07 UTC  
> Url: https://github.com/boostorg/gil/pull/565#issuecomment-787581055  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/565?src=pr&el=h1) Report  
> Merging [#565](https://codecov.io/gh/boostorg/gil/pull/565?src=pr&el=desc) (f49e78b) into [develop](https://codecov.io/gh/boostorg/gil/commit/0c0fe1ae79053d8aedfa95eca39329d135e4ea1c?el=desc) (0c0fe1a) will **increase** coverage by `0.77%`.  
> The diff coverage is `97.18%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #565      +/-   ##  
===========================================  
+ Coverage    77.81%   78.58%   +0.77%       
===========================================  
  Files          110      117       +7       
  Lines         4367     5002     +635       
===========================================  
+ Hits          3398     3931     +533       
- Misses         969     1071     +102       
```

---

## Comment 5

> Username: mloskot  
> Created_at: 2021-03-01 20:40:14 UTC  
> Url: https://github.com/boostorg/gil/pull/565#issuecomment-788259383  

@gkumar28 Please, hit "Resolve conversation" button for the comments that you have addressed, so we know what has been done.

---

## Comment 6

> Username: gkumar28  
> Created_at: 2021-03-01 21:48:07 UTC  
> Url: https://github.com/boostorg/gil/pull/565#issuecomment-788321411  

I have made the necessary changes. Please review them.

---

## Review 7 [Changes requested]

> Username: mloskot  
> Created_at: 2021-03-01 22:21:18 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/565#pullrequestreview-601219997  

Major issue to me is lack of test for this new feature

📁 include/boost/gil/extension/numeric/affine.hpp

```diff
 118 |+ ///           using consecutive affine transformations.
 119 |+ template<typename T, typename F> 
 120 |+     boost::gil::matrix3x2<F> center_rotate(boost::gil::point<T> dims,F rads)
```

> Username: mloskot  
> Created_at: 2021-03-01 22:20:37 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r585091637  

The four-space indentation of this line is unnecessary

---

📁 include/boost/gil/extension/numeric/affine.hpp

```diff
 122 |+     F PI = F(3.141592653589793238);
 123 |+     F c_theta = std::abs(std::cos(rads));
 124 |+     F s_theta = std::abs(std::sin(rads));
```

> Username: mloskot  
> Created_at: 2021-03-01 22:20:55 UTC  
> Updated_at: 2021-03-03 09:24:42 UTC  
> Url: https://github.com/boostorg/gil/pull/565#discussion_r585091806  

`const` and `constexpr` where possible


---

## Comment 8

> Username: mloskot  
> Created_at: 2021-03-03 20:06:23 UTC  
> Url: https://github.com/boostorg/gil/pull/565#issuecomment-790020416  

@lpranam I think this will conflict with your https://github.com/boostorg/gil/pull/573  
What do you suggest to do?

---

## Comment 9

> Username: lpranam  
> Created_at: 2021-03-03 22:20:58 UTC  
> Url: https://github.com/boostorg/gil/pull/565#issuecomment-790114515  

@mloskot fortunately I am yet to touch those files :D so if this PR is ready we should get this in

---
