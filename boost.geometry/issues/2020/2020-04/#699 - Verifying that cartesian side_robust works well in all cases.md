# #699 - Verifying that cartesian side_robust works well in all cases [Closed]

> Username: tinko92  
> Created at: 2020-04-19 14:41:40 UTC  
> Updated at: 2023-06-20 08:12:28 UTC  
> Closed at: 2023-06-20 08:12:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/699  

(Because boost is currently preparing for a release, let me preface this issue by stating that the following is not time-critical in any way.)  
  
I would like to submit a PR in the future to move side_robust ( https://github.com/boostorg/geometry/blob/e1521a2735f21f319b7f00929b78dc6f9a3dfdaf/include/boost/geometry/extensions/triangulation/strategies/cartesian/side_robust.hpp ) from extensions into the library proper. It is a robust cartesian side strategy with tuneable robustness/performance.   
  
In #690 it was mentioned by @barendgehrels that it has not been verified whether it works well for all cases. Because of that, I thought it might be sensible to submit an issue first, so that the matter of verifying that it works well for all cases can be discussed/possibly resolved.  
  
I thought about how to verify correctness and robustness and came up with the following test:  
  
https://gist.github.com/tinko92/c6d8b862b8e4b3188b0eea41cfdfb17d  
  
The gist of the gist is that the results of side_robust for points with  
- uniformly distributed double coordinates  
- coordinates with random magnitude  
- random coordinates on a grid, randomly perturbed by rotation and scale transformations  
are internally consistent and consistent with the results of Exact_predicates_exact_constructions_kernel from CGAL. So I am reasonably certain that the implementation is correct.  
  
What is not yet covered by side_robust, though, are underflow and overflow.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2020-04-22 08:18:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/699#issuecomment-617627235  

Hi @tinko92 , thanks for this, it is very interesting.  
Can you also compare the (speed) performance? Side is called a lot in our code, so we have to know how much slower (or faster, but robust is usually a bit slower) it is...

---

## Comment 2

> Username: tinko92  
> Created at: 2020-04-22 13:21:23 UTC  
> Updated at: 2020-04-22 15:24:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/699#issuecomment-617775379  

The robust side predicate is adaptive in the sense that it only performs higher precision arithmetic if deemed necessary after comparing the result to an error estimate. That means that for non-problematic input the added cost is 2 computations of std::abs, 1 addition, 1 multiplication, 1 comparison of two doubles, and 1 branch (if I didn't forget anything). This should be the added cost when compared to directly evaluating  (a.x - c.x) * (b.y - c.y) - (a.y - c.y) * (b.x - c.x) <=> 0.  
  
I devised a primitive benchmark that is based on the gist above. I added another class of examples, namely purposefully degenerate points that are specifically designed to trigger the most code path. I don't think they represent any real-world use case but they may provide an upper limit on the performance cost of robust predicates.  
  
**Benchmark Code**  
  
The gist with the benchmark code can be found here: https://gist.github.com/tinko92/a6a60f4a476406490985eb0047d122f9  
  
I will now provide tables with the results (except for random exponent, they look very similar to the results for random values). All times are in ms, all predicates are run 10,000,000 times:  
  
**Results in ms (lower is better)**  
  
Coordinates taken from uniform real distribution:  
  
|                            | side_by_triangle | side_robust[<,3>] | side_robust<,2> | side_robust<,1> | side_robust<,0> |  
|----------------------------|------------------|-------------------|-----------------|-----------------|-----------------|  
| Xeon E3-1505Mv5 GCC 9.3    | 242              | 150               | 155             | 153             | 84              |  
| Xeon E3-1505Mv5 Clang 10   | 239              | 240               | 229             | 238             | 36              |  
| Raspberry Pi (ARM) GCC 8.3 | 1562             | 749               | 429             | 425             | 276             |  
| Raspberry Pi (ARM) Clang 7 | 2146             | 606               | 613             | 600             | 284             |  
  
Perturbed grid  
|                            | side_by_triangle | side_robust[<,3>] | side_robust<,2> | side_robust<,1> | side_robust<,0> |  
|----------------------------|------------------|-------------------|-----------------|-----------------|-----------------|  
| Xeon E3-1505Mv5 GCC 9.3    | 234              | 155               | 164             | 154             | 85              |  
| Xeon E3-1505Mv5 Clang 10   | 248              | 246               | 253             | 257             | 42              |  
| Raspberry Pi (ARM) GCC 8.3 | 1563             | 750               | 431             | 425             | 275             |  
| Raspberry Pi (ARM) Clang 7 | 2118             | 603               | 613             | 601             | 238             |  
  
Purposefully Degenerate input (p1(1, 1), p2(1e20, std::nextafter(1e20, 1e21)), p3(1e40, 1e40))  
|                            | side_by_triangle | side_robust[<,3>] | side_robust<,2> | side_robust<,1> | side_robust<,0> |  
|----------------------------|------------------|-------------------|-----------------|-----------------|-----------------|  
| Xeon E3-1505Mv5 GCC 9.3    | 163              | 1034              | 215             | 148             | 32              |  
| Xeon E3-1505Mv5 Clang 10   | 173              | 1369              | 435             | 405             | 40              |  
| Raspberry Pi (ARM) GCC 8.3 | 1447             | 8977              | 1837            | 1143            | 240             |  
| Raspberry Pi (ARM) Clang 7 | 1982             | 9169              | 1736            | 1378            | 288             |  
  
**Notes**  
side_robust<double> is the same as side_robust<double, 3>, the 3 is the parameter for maximum robustness.  
side_robust<double, i> for i=0, 1 and 2 are included for the sake of completeness. With robustness-parameters 2 or 1, the result is computed such that it should be correct for determinants that are in O(eps^2) or O(eps) respectively. With parameter 0, the determinant is directly computed with no extra checks or extra precision.  
  
**conclusion**  
  
1. The validity of this benchmark is very limited. side_robust<...,3> should be strictly slower than side_robust<...,2> and so on, but the difference is apparently so small that it is masked by noise.  
2. side_robust is faster or as fast as side_by_triangle for random coordinates.  
3. For worst case inputs (i.e. p1(1, 1), p2(1e20, std::nextafter(1e20, 1e21)), p3(1e40, 1e40)) side_robust may be about one order of magnitude slower than side_by_triangle.  
  
@barendgehrels I know this benchmark is completely artificial. Do you have a large (as in computationally expensive), real-world problem for an Boost.Geometry algorithm that uses a side-predicate? Maybe that could provide further insights.

---

## Comment 3

> Username: tinko92  
> Created at: 2020-04-23 00:08:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/699#issuecomment-618102093  

After some further analysis, I was able to improve the performance of side_robust for non-degenerate input (see PR #701 ). For the sake of brevity, I will only include results for uniformly distributed points and purposefully degenerate points and only for the computer with the Xeon CPU:  
  
uniformly distributed points  
GCC 9.3: 93ms  
Clang 10: 65ms  
  
purposefully degenerate points  
GCC 9.3: 813ms  
Clang 10: 980ms  
  
Correctness is unaffected. The results for points with random exponents and for points on a perturbed grid are similar to uniformly distributed points. With these changes, side_robust is now more than twice as fast as side_by_triangle for inputs that are not intentionally created in a way that triggers the most expensive code path.  
  
I believe the only question that is really matters when it comes to the performance penalty for robustness is what the execution path looks like for non-degenerate input:   
  
- In the case of side_by_triangle the points are tested for equality ( https://github.com/boostorg/geometry/blob/56a9f79036dc3bce8dcd0483cfa728a196997f81/include/boost/geometry/strategies/cartesian/side_by_triangle.hpp#L178 ) requiring six comparisons in the most common case, implicitly sorted, which requires 4 comparisons and leads to four different execution paths which seem all equally likely. And then there is some additional cost for applying the epsilon_policy.  
  
- In the case of side_robust, the additional cost is limited to three evaluations of std::abs, one additional, one multiplication and one comparison ( From https://github.com/boostorg/geometry/blob/4a3184122ae0e5e4a5e757097b59a9e042ec7c38/include/boost/geometry/extensions/triangulation/strategies/cartesian/detail/precise_math.hpp#L339 to line 346 ). The alternative execution path (after line 349 in the linked file) is extremely unlikely, so this should work well with speculative execution.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2020-04-27 10:30:27 UTC  
> Updated at: 2020-04-27 10:31:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/699#issuecomment-619890102  

> @barendgehrels I know this benchmark is completely artificial. Do you have a large (as in computationally expensive), real-world problem for an Boost.Geometry algorithm that uses a side-predicate? Maybe that could provide further insights.  
  
Yes, we have several benchmark tests. `star_comb` and `recursive_polygons` are useful ones. They are in `.../test/robustness/overlay/areal_areal/` They call `side_by_triangle` a lot, and from several locations. So probably the most convenient is (temporarily) call your code from there, instead (conditionally) and compare behaviour.  
  
There are boost program options, so you can see how it can be called (`--help`). I just called it like this: `star_comb --count 10 --point_count 100` and this gives me 12 seconds (on an old computer).  
  
It might be that you hardly detect any differences, because calculating the turns themselves is usually the most time consuming, and sides are not involved there (or hardly). As far as I know we don't have tests relying only on side performance (would be good to have them, actually).

---

## Comment 5

> Username: tinko92  
> Created at: 2020-04-27 20:15:10 UTC  
> Updated at: 2020-04-28 04:46:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/699#issuecomment-620209327  

Thanks, I was unaware of those. I ran them with --count 50 --point_count 100 and --count 10000 respectively on a machine with a XEON E3-1505Mv5, GCC 9.3, here are my results:  
  
|                                                                                 | recursive_polygons | star_comb |  
|---------------------------------------------------------------------------------|--------------------|-----------|  
| default                                                                         | 7.32641            | 16.2523   |  
| BOOST\_GEOMETRY\_NO\_ROBUSTNESS                                                 | 6.39251            | 16.0254   |  
| BOOST\_GEOMETRY\_NO\_ROBUSTNESS and side\_by\_triangle replaced by side\_robust | 5.82279            | 15.8445   |  
  
Repeated runs gave similar results.  
  
With regard to tests that rely on side performance, maybe some time in the future, if my triangulation algorithm is merged, there will be one. I checked out out my triangulation branch and cherry-picked the performance improvement commit from the other branch. Here are the results:  
  
Triangulation of 10,000,000 points:  
side_by_triangle: 10937ms.  
robust: 8700ms.

---

## Comment 6

> Username: barendgehrels  
> Created at: 2020-04-29 11:44:55 UTC  
> Updated at: 2020-04-29 11:46:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/699#issuecomment-621149602  

> here are my results:  
  
Very good! OK so it is not only better but also a bit faster, that is really good news!  
Thanks for your tests!  
  
So we can replace side by triangle with your approach, in 1.75. That is basically fine indeed. With respect to my own actions (removing rescaling), because this side is more robust, it might help there too. So I will check that (after my current buffer actions, so probably 2 weeks from now) and probably just use it. The rescaling removal takes at least one other release, so for that 1.75 is probably just fine too.

---

## Comment 7

> Username: vissarion  
> Created at: 2020-05-07 08:05:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/699#issuecomment-625098523  

@tinko92 great!   
  
@barendgehrels I think it would be better instead of replacing to have two options: robust/slow, non-robust/fast, maybe two different strategies. Obviously the current non-robust implementation is not that fast but this should be able to get fixed. On way would be to use the implementation of robust predicate and let the user parameterize it to have a fast and not-so-robust version of the predicate.

---

## Comment 8

> Username: tinko92  
> Created at: 2020-05-12 01:25:53 UTC  
> Updated at: 2020-05-12 14:21:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/699#issuecomment-627053212  

To address the suggestion to have two versions (robust and fast), I have performed one more set of synthetic benchmarks to assess the performance/robustness trade-off in the raw evaluation of the orientation predicate. Thanks @vissarion for the advice to compare to the fast CGAL predicate, it made me realize that side_robust<double, 0> was not optimal as I believed before the benchmark:  
  
times given in average ns per evaluation (over 1e8 triplets of input points):  
  
uniform random coordinates:  
side_by_triangle: 21.64  
side_robust<>: 8.24  
side_robust<,0>: 8.00  
minimal: 7.04  
CGAL (CGAL::Exact_predicates_inexact_constructions_kernel): 15.90  
CGAL (CGAL::Simple_cartesian): 7.52  
  
For uniform random coordinates, all predicates return correct values.  
  
perturbed grid (some degenerate inputs):  
side_by_triangle: 22.69  
side_robust<>: 8.22  
side_robust<,0>: 8.15  
minimal: 7.03  
CGAL (CGAL::Exact_predicates_inexact_constructions_kernel): 16.11  
CGAL (CGAL::Simple_cartesian): 7.45  
  
For the perturbed grid only side_robust<> and CGAL with exact predicates return the correct side values.  
  
The code for the minimal predicate is:  
  
```  
struct minimal  
{  
        template<typename Point>  
        static inline int apply(Point const& p1, Point const& p2, Point const& p) {  
                auto detleft = (bg::get<0>(p1) - bg::get<0>(p)) * (bg::get<1>(p2) - bg::get<1>(p));  
                auto detright = (bg::get<1>(p1) - bg::get<1>(p)) * (bg::get<0>(p2) - bg::get<0>(p));  
                return detleft > detright ? 1 : (detleft < detright ? -1 : 0 );  
        }  
};  
```  
  
Surprisingly (for me) the fact that the actual determinant (which would be detleft - detright) is not computed, saves ~1/8th of the computation time.  
  
I also checked recursive polygons benchmark again and saw a slight speedup for the minimal predicate (5.48s vs 5.82s for the robust predicate).  
  
So in conclusion, I think one could offer two predicates, a minimal one and a robust one, with the robust one being ~16% slower but guaranteeing correct results even for degenerate inputs.  
  
(_Edited to include results for side_by_triangle and to use the proper names of the CGAL kernels_).

---

## Comment 9

> Username: vissarion  
> Created at: 2020-05-12 07:28:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/699#issuecomment-627164976  

Thanks @tinko92 for those benchmarks! For completeness could you please include also the two versions of `side_by_triangle` on this table. I guess will be slower than side_rodust but faster than exact CGAL. What about correctness?  
  
Also I guess that "CGAL (exact predicate kernel)" means [Exact_predicates_inexact_constructions_kernel](https://doc.cgal.org/latest/Kernel_23/classCGAL_1_1Exact__predicates__inexact__constructions__kernel.html) and "simple cartesian kernel" is [that](https://doc.cgal.org/latest/Kernel_23/structCGAL_1_1Simple__cartesian.html). It seems that [this CGAL kernel](https://doc.cgal.org/latest/Kernel_23/structCGAL_1_1Cartesian.html) is faster since it does not copy objects, so should perform closer to your "minimal" predicate.

---

## Comment 10

> Username: tinko92  
> Created at: 2020-05-12 14:18:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/699#issuecomment-627374855  

Your guess is correct, I'll edit the above comment to be more precise. I tried CGAL::Cartesian but for some reason it was slightly slower than CGAL::Simple_cartesian. This might be a limitation with my benchmark code or I might be missing some define that should be used (I currently only define NDEBUG).  
  
Here are side_by_triangle-times for comparison:  
uniform random coordinates: 21.64ns  
perturbed grid: 22.69ns  
  
With respect to correctness, I only record the sum of all results and consider it correct if it matches the result from CGAL::Exact_predicates_inexact_constructions_kernel. By that definition of correctness:  
All predicates are always correct for uniform random coordinates.  
Only Exact_predicates_inexact_constructions_kernel and side_robust<> are always correct for the perturbed grid points. I'll add all results to my earlier comment.
