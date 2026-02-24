# #585 Add rasterizer support for ellipse [Merged]

> Username: meshtag  
> Created at: 2021-03-23 04:23:19 UTC  
> Updated at: 2022-06-26 17:01:10 UTC  
> Merged at: 2021-03-28 19:00:12 UTC  
> Closed at: 2021-03-28 19:00:12 UTC  
> Url: https://github.com/boostorg/gil/pull/585  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
This pull request adds rasterization functionality for drawing ellipse using midpoint algorithm.   
  
I would like to ponder upon following points once this basic functionality is approved(possibly merged) :   
 - Add support for drawing a rotated ellipse   
 - Vary thickness of curves drawn using rasterization  
 - Draw arcs(circular or elliptical) instead of drawing complete curves   
  
I would like this pull request to be considered as solution for my [competency test](https://github.com/boostorg/wiki/wiki/Google-Summer-of-Code:-2021#boostgil-generic-image-library).  
### References  
  
- An Efficient Ellipse-Drawing Algorithm, September 1984, pp. 24-35, vol. 4, by [Jerry Van Aken](https://www.computer.org/csdl/search/default?type=author&givenName=Jerry&surname=Van%20Aken), https://doi.ieeecomputersociety.org/10.1109/MCG.1984.275994  
  
  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-03-23 05:17:23 UTC  
> Updated_at: 2021-03-27 05:30:28 UTC  
> Url: https://github.com/boostorg/gil/pull/585#issuecomment-804623783  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/585?src=pr&el=h1) Report  
> Merging [#585](https://codecov.io/gh/boostorg/gil/pull/585?src=pr&el=desc) (b698949) into [develop](https://codecov.io/gh/boostorg/gil/commit/b8564e256c067275287792a1678a74cf81d4ae08?el=desc) (b8564e2) will **increase** coverage by `0.13%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #585      +/-   ##  
===========================================  
+ Coverage    78.59%   78.72%   +0.13%       
===========================================  
  Files          117      118       +1       
  Lines         5003     5034      +31       
===========================================  
+ Hits          3932     3963      +31       
  Misses        1071     1071                
```

---

## Review 2 [Commented]

> Username: mloskot  
> Created_at: 2021-03-23 08:57:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/585#pullrequestreview-618340080  

📁 include/boost/gil/rasterization/ellipse.hpp

```diff
  27 |+     /// \param semi_axes - Array containing half of lengths of horizontal and vertical axis respectively.
  28 |+     std::vector<std::array<std::ptrdiff_t, 2>> obtain_trajectory(
  29 |+                                                     const std::array<unsigned int, 2> semi_axes)
```

> Username: mloskot  
> Created_at: 2021-03-23 08:57:24 UTC  
> Updated_at: 2021-03-27 03:49:55 UTC  
> Url: https://github.com/boostorg/gil/pull/585#discussion_r599379103  

Please, consider trailing return and eastconst too  
  
```  
auto obtain_trajectory(std::array<unsigned int, 2> const semi_axes)  
    -> std::vector<std::array<std::ptrdiff_t, 2>>  
```


---

## Review 3 [Commented]

> Username: mloskot  
> Created_at: 2021-03-23 09:01:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/585#pullrequestreview-618343796  

📁 include/boost/gil/rasterization/ellipse.hpp

```diff
  29 |+                                                     const std::array<unsigned int, 2> semi_axes)
  30 |+     {
  31 |+         // Citation : J. Van Aken, "An Efficient Ellipse-Drawing Algorithm" in IEEE Computer Graphics and Applications, vol. 4, no. 09, pp. 24-35, 1984.
```

> Username: mloskot  
> Created_at: 2021-03-23 09:01:21 UTC  
> Updated_at: 2021-03-27 03:49:55 UTC  
> Url: https://github.com/boostorg/gil/pull/585#discussion_r599381972  

Does this citation mean your algorithm is based on this paper?

> Username: meshtag  
> Created_at: 2021-03-23 12:31:41 UTC  
> Updated_at: 2021-03-27 03:49:55 UTC  
> Url: https://github.com/boostorg/gil/pull/585#discussion_r599524377  

Yes, algorithm for obtaining trajectory points used inside ```obtain_trajectory()``` is based on the mentioned paper.


---

## Review 4 [Changes requested]

> Username: mloskot  
> Created_at: 2021-03-23 09:07:22 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/585#pullrequestreview-618345659  

Please, ensure this code does not introduce any **new** warnings

📁 include/boost/gil/rasterization/ellipse.hpp

```diff
  42 |+         t1 = semi_axes[0] * semi_axes[0], t2 = 2 * t1, t3 = 2 * t2;
  43 |+         t4 = semi_axes[1] * semi_axes[1], t5 = 2 * t4, t6 = 2 * t5;
  44 |+         t7 = semi_axes[0] * t5, t8 = 2 * t7, t9 = 0;
```

> Username: mloskot  
> Created_at: 2021-03-23 09:03:24 UTC  
> Updated_at: 2021-03-27 03:49:55 UTC  
> Url: https://github.com/boostorg/gil/pull/585#discussion_r599383301  

I believe these three are not modified anywhere, so why not 'document' that declaring them as constants  
```cpp  
long long int const t1 = ...;  
long long int const t4 = ...;  
long long int const t7 = ...;  
```

---

📁 include/boost/gil/rasterization/ellipse.hpp

```diff
  98 |+     void draw_curve(View view, const std::vector<unsigned int> colour,
  99 |+     const std::array<unsigned int, 2> center,
 100 |+     const std::vector<std::array<std::ptrdiff_t, 2>> trajectory_points)
```

> Username: mloskot  
> Created_at: 2021-03-23 09:03:53 UTC  
> Updated_at: 2021-03-27 03:49:55 UTC  
> Url: https://github.com/boostorg/gil/pull/585#discussion_r599383656  

4 space indentation of the arguments, eastconst would be nice too.

---

📁 include/boost/gil/rasterization/ellipse.hpp

```diff
 100 |+     const std::vector<std::array<std::ptrdiff_t, 2>> trajectory_points)
 101 |+     {
 102 |+         for(std::size_t i = 0, colour_index = 0;i < view.num_channels(); ++i, ++colour_index)
```

> Username: mloskot  
> Created_at: 2021-03-23 09:04:12 UTC  
> Updated_at: 2021-03-27 03:49:55 UTC  
> Url: https://github.com/boostorg/gil/pull/585#discussion_r599383895  

Nitpick: `0;i` is missing space

---

📁 include/boost/gil/rasterization/ellipse.hpp

```diff
 179 |+ }; // midpoint elliptical rasterizer
 180 |+ }} // namespace boost::gil
 181 |+ #endif
```

> Username: mloskot  
> Created_at: 2021-03-23 09:04:41 UTC  
> Updated_at: 2021-03-27 03:49:55 UTC  
> Url: https://github.com/boostorg/gil/pull/585#discussion_r599384200  

newline  missing


📁 test/core/rasterization/ellipse.cpp

```diff
  21 |+ {
  22 |+     float focus_x, focus_y;
  23 |+     if(a > b) // For horizontal ellipse
```

> Username: mloskot  
> Created_at: 2021-03-23 09:04:56 UTC  
> Updated_at: 2021-03-27 03:49:55 UTC  
> Url: https://github.com/boostorg/gil/pull/585#discussion_r599384388  

Nitpick: `if(` is missing space, here and in more places and in `for(` too

---

📁 test/core/rasterization/ellipse.cpp

```diff
  18 |+ // Parameters b and a represent half of lengths of vertical and horizontal axis respectively.
  19 |+ void test_rasterizer_follows_equation(std::vector<std::array<std::ptrdiff_t, 2>> trajectory_points, 
  20 |+                                                                                     int a, int b)
```

> Username: mloskot  
> Created_at: 2021-03-23 09:05:40 UTC  
> Updated_at: 2021-03-27 03:49:55 UTC  
> Url: https://github.com/boostorg/gil/pull/585#discussion_r599384912  

Please, don't run with the arguments to the right.  
Humans usually read from left to right, so we prefer to align to left, e.g.   
  
```cpp  
void test_rasterizer_follows_equation(  
    std::vector<std::array<std::ptrdiff_t, 2>> trajectory_points,   
    int a, int b)  
```


---

## Review 5 [Approved]

> Username: mloskot  
> Created_at: 2021-03-26 21:31:44 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/585#pullrequestreview-622546948  

The feature is based on a known paper and is covered by a test.  
So, without doing any verification of the algorithm implementation, I'm happy to accept it.  
  
/cc @lpranam any objections?

---

## Review 6 [Changes requested]

> Username: lpranam  
> Created_at: 2021-03-28 08:16:32 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/585#pullrequestreview-622730438  

you forgot to add example in CMakeLists.txt otherwise all good :D

---

## Comment 7

> Username: meshtag  
> Created_at: 2021-03-28 09:42:14 UTC  
> Url: https://github.com/boostorg/gil/pull/585#issuecomment-808872363  

As far as I understand, we don't need to add example files individually in example/CMakeLists.txt since it is using ```GLOB_RECURSE``` (unlike some other CMakeLists.txt files of the project).  
Do you want me to change any other file?  Please correct me if I am wrong.

---

## Comment 8

> Username: lpranam  
> Created_at: 2021-03-28 10:08:05 UTC  
> Url: https://github.com/boostorg/gil/pull/585#issuecomment-808875151  

oh yes, sorry my bad I forgot. we have a few different approach in cmake so this happens a lot with me...

---

## Comment 9

> Username: marco-langer  
> Created_at: 2022-06-02 04:05:31 UTC  
> Updated_at: 2022-06-02 04:09:14 UTC  
> Url: https://github.com/boostorg/gil/pull/585#issuecomment-1144412220  

This PR was staged for releasing with Boost 1.80, but I'd like to give another review on ellipse.hpp:  
  
* naming: why is it midpoint_circle_rasterizer, but midpoint_elliptical_rasterizer?  
* the circle rasterizers take the center and radius arguments as signed integers whereas the ellipse rasterizer uses unsigned integers for semi-axes and center  
* line and circle rasterizers implement the call operator to obtain the trajectory whereas the ellipse rasterizer implements the call operator for drawing the trajectory to an image  
* the call operator makes a deep copy of the trajectory, because draw_curve() takes the vector by value  
* why is the color implemented as a `std::vector<unsigned int>` and not as a pixel?  
* why is error handling printed to std::cout?

---

## Comment 10

> Username: meshtag  
> Created_at: 2022-06-02 05:16:31 UTC  
> Url: https://github.com/boostorg/gil/pull/585#issuecomment-1144445685  

Thanks a lot for providing your review @marco-langer !  
  
> naming: why is it midpoint_circle_rasterizer, but midpoint_elliptical_rasterizer?  
  
 - naming : I agree that we can follow a more consistent naming pattern (maybe `midpoint_ellipse_rasterizer` instead of `midpoint_elliptical_rasterizer` ?)  
  
> the circle rasterizers take the center and radius arguments as signed integers whereas the ellipse rasterizer uses unsigned integers for semi-axes and center  
  
 - There is no significant point in taking semi-axes as signed integers. For center co-ordinates, I think we can chose to provide signed integer support here (though it might require significant changes in some other parts of the algorithm as well, for ex. checking of curve overflow in our frame). I can share more on this once I test/observe the usefulness of signed center co-ordinates in the case of `midpoint_circle_rasterizer` as well.  
  
> line and circle rasterizers implement the call operator to obtain the trajectory whereas the ellipse rasterizer implements the call operator for drawing the trajectory to an image  
  
 - I think my intention here was to save the user from the hassle of drawing stuff (with overflow checking).  
  
> the call operator makes a deep copy of the trajectory, because draw_curve() takes the vector by value  
  
 - Yes, I think we can probably try passing it by reference  
  
> why is the color implemented as a std::vector<unsigned int> and not as a pixel?  
  
 - I don't really remember the reason I used `std::vector<unsigned int>` for color  
  
> why is error handling printed to std::cout?  
  
 - I don't remember the reason for using `std::cout` for indicating curve overflow, (perhaps I hesitated from using `std::length_error` because curve overflow is more like a warning/notification than an actual error). Please feel free to share your suggestions here as well.

---

## Comment 11

> Username: mloskot  
> Created_at: 2022-06-02 07:33:43 UTC  
> Updated_at: 2022-06-02 07:34:52 UTC  
> Url: https://github.com/boostorg/gil/pull/585#issuecomment-1144543380  

@marco-langer Great comments. Since this has already been merged, I'd suggest to simply open an issue with reference to this PR.  
Unless @meshtag wants to attack your suggestions sooner and submit PR :)

---

## Comment 12

> Username: mloskot  
> Created_at: 2022-06-26 11:34:56 UTC  
> Url: https://github.com/boostorg/gil/pull/585#issuecomment-1166503512  

@meshtag FYI, we have merged PR #692 which addresses points raised by @marco-langer, so please have a look at those changes.

---

## Comment 13

> Username: meshtag  
> Created_at: 2022-06-26 17:01:10 UTC  
> Url: https://github.com/boostorg/gil/pull/585#issuecomment-1166588156  

Reviewed the PR.  
@mloskot , thanks a lot for informing!

---
