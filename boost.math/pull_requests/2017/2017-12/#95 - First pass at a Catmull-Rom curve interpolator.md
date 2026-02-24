# #95 First pass at a Catmull-Rom curve interpolator. [Merged]

> Username: NAThompson  
> Created at: 2017-12-21 23:15:05 UTC  
> Updated at: 2018-12-04 20:05:29 UTC  
> Merged at: 2018-12-04 19:27:37 UTC  
> Closed at: 2018-12-04 19:27:37 UTC  
> Url: https://github.com/boostorg/math/pull/95  

I'm putting this PR out here even though it's a bit half baked just to shame myself into finishing it.  
  
Take a quick look, see if y'all think it's a good addition to boost.math.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2017-12-24 00:15:37 UTC  
> Url: https://github.com/boostorg/math/pull/95#issuecomment-353755971  

Ok, I guess throwing out total garbage as a PR works, because now I think I've patched it up pretty good. The only concerns I have are:  
  
- It doesn't work with the Boost.geometry.point, because those use `get<0>(pnt)` syntax, rather than `pnt[0]` syntax.  
- The template parameters are redundant, in the sense that the type of each element of a `Point` must be `Real`,  and the length of each point must be `dimension`. So arguably there exists a way to only have one template parameter.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2018-10-29 17:05:23 UTC  
> Url: https://github.com/boostorg/math/pull/95#issuecomment-433993673  

The build looks largely good. `g++-6 --std=c++1z` [fails](https://travis-ci.org/boostorg/math/jobs/447629613) with an internal compiler error on travis, which boggles my mind, because the C++17 feature check returns false, so none of the Catmull-Rom code should even be compiled. The [64-bit MSVC 14](https://ci.appveyor.com/project/jzmaddock/math/builds/19869485/job/5pr37l97lj4850uq) build fails due to exp-sinh quadrature, which is confusing, as I rebased this. [Another failure](https://ci.appveyor.com/project/jzmaddock/math/builds/19869485/job/eh62vmlqtw25ba7c) is due to a timeout.  
  
In any case, SVG-2 syntax will be providing an option to build centripetal Catmull-Rom splines, so adding this might be a nice feature for renderers.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2018-12-02 09:24:17 UTC  
> Url: https://github.com/boostorg/math/pull/95#issuecomment-443493454  

Hi Nick, I've merged develop to this to fix some conflicts and also fixed some formatting issues in the docs relating to the removal of [br]'s (that was surprisingly hard to get right!).  
  
I have some mostly cosmetic suggestions/questions:  
  
* The range of the parameter is always [0, max_parameter()] ?  Why not always [0,1] for example?  
* Is 0 always a valid point when closed/open?  
* alpha should be documented to be in [0, 1].  
* The meaning of closed - when I read this first for some reason I took this to mean only a change in the behaviour of the endpoints, but closed actually means a closed figure (as in circle)?  Sorry if I'm being dense, but that's the only way I can see interpolating at max_parameter() giving the first point.  
* Performance section - am I right that the intention is to show how performance changes with the number of points - ie big-O behaviour?  Can you make this a bit clearer in the preamble to the table?  
* Constructors: can we have an initializer list constructor?  How about from an iterator range or some other range/sequence type (cf last polynomial changes).  
* The point type: should std::pair or std::tuple be usable as points?  Yes I know we have std::array, I'm wondering if the concept of point should be abstracted to non-member accessors that can be overloaded for any suitable type (the defaults would use operator[] as now, with overloads for other std lib types like pairs and tuples).  That way the point class can be pretty much anything the user wants, and modifications to interoperate with catmull_rom are entirely non-intrusive.  
  
That's all for now, best, John.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2018-12-02 18:16:34 UTC  
> Url: https://github.com/boostorg/math/pull/95#issuecomment-443529776  

> The range of the parameter is always [0, max_parameter()] ? Why not always [0,1] for example?  
  
It is mainly a desire to follow the reference  [Parametrization and Applications of Catmull-Rom Curves](http://www.cemyuksel.com/research/catmullrom_param/catmullrom_cad.pdf), equation 1. Certainly it is possible to linearly rescale to `[0,1]`, but suppose that the user chooses `alpha = 1`, the chordal parameterization. Then the parameter is the total length of the curve, which seems intuitive and shouldn't be rescaled to `[0,1]`. The length of the tangent vector would also be meaningful in this case, and if we rescaled we'd need to figure out how to recover the proper norm. But I think you are correct that for most use cases, rescaling to `[0,1]` would be just fine.  
  
> Is 0 always a valid point when closed/open?  
  
Yes, I achieved this by making the first segment have negative parametrization. And `interpolant(0)` always returns the first point in the list of points you pass to the constructor. This is now documented.  
  
> alpha should be documented to be in [0, 1].  
  
Yes, this is now done.  
  
> Performance section - am I right that the intention is to show how performance changes with the number of points - ie big-O behaviour? Can you make this a bit clearer in the preamble to the table?  
  
Done. Specifically the evaluation is O(log(N)).  
  
> Constructors: can we have an initializer list constructor? How about from an iterator range or some other range/sequence type (cf last polynomial changes).  
  
Yes; I simply forwarded the list to the move constructor. Really what should be done is every constructor should call the move constructor, which should (nondestructively) resize its input data and then do in-place moves. This would be much faster than the current implementation. (I had originally thought that the constructor didn't need to be fast but evaluation did. Now I think there could be a lot of use cases where the curve itself is not very long-lived; maybe representing some ammo trajectory in a video game. In this case you'd want the constructor to be as fast as possible as well.)  
  
> The point type: should std::pair or std::tuple be usable as points? Yes I know we have std::array, I'm wondering if the concept of point should be abstracted to non-member accessors that can be overloaded for any suitable type (the defaults would use operator[] as now, with overloads for other std lib types like pairs and tuples). That way the point class can be pretty much anything the user wants, and modifications to interoperate with catmull_rom are entirely non-intrusive.  
  
This was the biggest pain point for me. I would definitely like to do this, but the overload might require quite a lot of code duplication? Doing this well is really at the frontier of my C++ skillset.  
  
Even more generic would be templating on a `RandomAccessContainer`, whose points are the `value_type` of the container, and whose scalar type is the `value_type` of the `value_type`. I considered doing this but I was not convinced that the abstraction would actually generate any wins.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2018-12-02 20:36:02 UTC  
> Url: https://github.com/boostorg/math/pull/95#issuecomment-443539746  

> Really what should be done is every constructor should call the move constructor, which should (nondestructively) resize its input data and then do in-place moves. This would be much faster than the current implementation.  
  
This is now done; it is indeed much faster.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2018-12-03 05:00:44 UTC  
> Url: https://github.com/boostorg/math/pull/95#issuecomment-443589615  

For some reason didn't notice this the first time around:  
  
> The meaning of closed - when I read this first for some reason I took this to mean only a change in the behaviour of the endpoints, but closed actually means a closed figure (as in circle)? Sorry if I'm being dense, but that's the only way I can see interpolating at max_parameter() giving the first point.  
  
It represents both a change in the behavior of the endpoints and generates a closed figure. Most authors leave the first segment (between the first and second points) undefined for the open curve. This is because the first point is implicitly connected to zero, which makes bizarre tangent vectors in the first segment. Then they interpret the first point as a control point. Well, that's kinda painful for the user; how do you pick meaningless control points? Well, if you make a closed figure, then the last point is the control point for the first segment, and that's nice and intuitive, since you've got a closed loop.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2018-12-03 18:25:12 UTC  
> Url: https://github.com/boostorg/math/pull/95#issuecomment-443814920  

>It is mainly a desire to follow the reference Parametrization and Applications of Catmull-Rom Curves, equation 1. Certainly it is possible to linearly rescale to [0,1], but suppose that the user chooses alpha = 1, the chordal parameterization. Then the parameter is the total length of the curve, which seems intuitive and shouldn't be rescaled to [0,1]  
  
That's fine: as long there's a good reason for it ;)  
  
>This was the biggest pain point for me. I would definitely like to do this, but the overload might require quite a lot of code duplication? Doing this well is really at the frontier of my C++ skillset.  
  
It's something that could be extended later if required.  
  
>Even more generic would be templating on a RandomAccessContainer, whose points are the value_type of the container, and whose scalar type is the value_type of the value_type. I considered doing this but I was not convinced that the abstraction would actually generate any wins.  
  
Nod.  I guess we could make the storage external to the class so folks could use custom containers and allocators.  Actually, thinking about it, using custom allocators which for example use stack space for storage might be a big win for some - depends how expensive memory allocation is compared to crunching the numbers?  
  
I guess one easy change would be to add a default template argument for the storage:  
  
```  
template <class T>  
using vector_default_storage = std::vector<T>;  
  
template <class Point, template<typename> typename Storage = vector_default_storage>  
struct catmull_rom   
{  
   typedef Storage<Point> storage_type;  
   // ... etc  
};  
```  
  
>Well, if you make a closed figure, then the last point is the control point for the first segment, and that's nice and intuitive, since you've got a closed loop.  
  
Doesn't that result in strange behaviour if the first and last points are far apart, especially for the chordal parametrisation?  Or can the user just treat the first and last points as control points and ignore those segments anyway if they wish?

---

## Comment 8

> Username: NAThompson  
> Created_at: 2018-12-03 19:12:43 UTC  
> Url: https://github.com/boostorg/math/pull/95#issuecomment-443832590  

> Doesn't that result in strange behaviour if the first and last points are far apart, especially for the chordal parametrisation?  
  
Yes, but the beauty of using compactly supported basis functions is that the weird behavior is confined to single segments. As to always using the last point as the control for the first segment, well, the traditional alternative is using zero, which breaks affine invariance. There are really no great options here; maybe one of the best options is to recommend to the user that they define "reasonable control points" as the first and last points.  
  
> Or can the user just treat the first and last points as control points and ignore those segments anyway if they wish?  
  
The user can certainly do that. Another option is that we add two more points to  the user's data: For example, form a line between the first and second points, and place our first point along the line before the first point. Then do the same for the last point. Then the whole issue is resolved, but maybe not resolved in a way people expect, so you'd have to make it an option. But for some reason, that feels like optionitis, not design.  
  
>  I guess we could make the storage external to the class so folks could use custom containers and allocators. Actually, thinking about it, using custom allocators which for example use stack space for storage might be a big win for some - depends how expensive memory allocation is compared to crunching the numbers?  
  
I have some numbers on that: This is performance of the in-place move constructor, not counting the allocation of the original vector:  
  
```bash  
BMCatmullRomConstructor<float>/4             181 ns        181 ns    3808943  
BMCatmullRomConstructor<float>/8             196 ns        195 ns    3418987  
BMCatmullRomConstructor<float>/16            224 ns        224 ns    3127891  
BMCatmullRomConstructor<float>/32            293 ns        292 ns    2391053  
BMCatmullRomConstructor<float>/64            507 ns        507 ns    1368096  
BMCatmullRomConstructor<float>/128           797 ns        796 ns     876304  
BMCatmullRomConstructor<float>/256          1224 ns       1224 ns     547268  
BMCatmullRomConstructor<float>/512          2333 ns       2332 ns     298818  
BMCatmullRomConstructor<float>/1024         4522 ns       4517 ns     154047  
BMCatmullRomConstructor<float>/2048         8939 ns       8932 ns      77608  
BMCatmullRomConstructor<float>/4096        17851 ns      17840 ns      39010  
BMCatmullRomConstructor<float>/8192        35507 ns      35494 ns      19678  
BMCatmullRomConstructor<float>/16384       71737 ns      71694 ns       9625  
BMCatmullRomConstructor<float>/32768      148360 ns     148280 ns       4662  
BMCatmullRomConstructor<float>_BigO         4.49 N       4.49 N  
BMCatmullRomConstructor<float>_RMS             3 %          3 %  
BMCatmullRomConstructor<double>/4            199 ns        199 ns    3386714  
BMCatmullRomConstructor<double>/8            214 ns        214 ns    3276663  
BMCatmullRomConstructor<double>/16           266 ns        266 ns    2635959  
BMCatmullRomConstructor<double>/32           418 ns        418 ns    1676057  
BMCatmullRomConstructor<double>/64           620 ns        620 ns    1109139  
BMCatmullRomConstructor<double>/128          995 ns        994 ns     702388  
BMCatmullRomConstructor<double>/256         1831 ns       1830 ns     381029  
BMCatmullRomConstructor<double>/512         3580 ns       3577 ns     198419  
BMCatmullRomConstructor<double>/1024        7008 ns       7005 ns      98669  
BMCatmullRomConstructor<double>/2048       13972 ns      13966 ns      49838  
BMCatmullRomConstructor<double>/4096       27877 ns      27846 ns      25110  
BMCatmullRomConstructor<double>/8192       57077 ns      57013 ns      12157  
BMCatmullRomConstructor<double>/16384     117600 ns     117540 ns       5663  
BMCatmullRomConstructor<double>/32768     237069 ns     236992 ns       2925  
BMCatmullRomConstructor<double>_BigO        7.21 N       7.20 N  
BMCatmullRomConstructor<double>_RMS            2 %          2 %  
```  
  
If the move constructor has to realloc, then add about 30% to these numbers.  
For reference, the calls to the interpolator require about 20-30ns, with log(N) scaling.  
  
Is this information helpful? Well, regrettably, I don't know because the allocation is happening outside the benchmark. Will an "average" use fit into a stack allocation? That would be amazing, but it's unclear to me that it will be sufficiently common that it should be supported. In any case, if we templated on a `RandomAccessContainer`, then the call to `resize()` in the constructor could mean "add more stack space".  
  
  
> I guess one easy change would be to add a default template argument for the storage  
  
The advantage of your default-storage solution over my template on `RandomAccessContainer` is that the initializer list constructor is easy to implement under your strategy, but hard with a generic `RandomAccessContainer`.

---

## Comment 9

> Username: NAThompson  
> Created_at: 2018-12-03 19:19:20 UTC  
> Updated_at: 2018-12-03 19:19:59 UTC  
> Url: https://github.com/boostorg/math/pull/95#issuecomment-443834940  

For reference, here's the performance of the constructor with the time it takes to allocate and initialize:  
  
```bash  
BMCatmullRomConstructor<float>/4             464 ns        456 ns    1266189  
BMCatmullRomConstructor<float>/8             518 ns        517 ns    1284804  
BMCatmullRomConstructor<float>/16            576 ns        576 ns    1203949  
BMCatmullRomConstructor<float>/32            757 ns        748 ns     989861  
BMCatmullRomConstructor<float>/64            993 ns        984 ns     638488  
BMCatmullRomConstructor<float>/128          1437 ns       1431 ns     431173  
BMCatmullRomConstructor<float>/256          2457 ns       2453 ns     280381  
BMCatmullRomConstructor<float>/512          4545 ns       4542 ns     152512  
BMCatmullRomConstructor<float>/1024         8759 ns       8754 ns      78887  
BMCatmullRomConstructor<float>/2048        17768 ns      17760 ns      38436  
BMCatmullRomConstructor<float>/4096        35402 ns      35318 ns      20011  
BMCatmullRomConstructor<float>/8192        71327 ns      71234 ns       9659  
BMCatmullRomConstructor<float>/16384      143776 ns     143685 ns       4817  
BMCatmullRomConstructor<float>/32768      293367 ns     293236 ns       2350  
BMCatmullRomConstructor<float>_BigO         8.90 N       8.90 N  
BMCatmullRomConstructor<float>_RMS             2 %          2 %  
BMCatmullRomConstructor<double>/4            512 ns        512 ns    1344628  
BMCatmullRomConstructor<double>/8            521 ns        520 ns    1265777  
BMCatmullRomConstructor<double>/16           622 ns        603 ns    1233545  
BMCatmullRomConstructor<double>/32           729 ns        725 ns     962014  
BMCatmullRomConstructor<double>/64           965 ns        964 ns     686746  
BMCatmullRomConstructor<double>/128         1515 ns       1510 ns     460866  
BMCatmullRomConstructor<double>/256         2638 ns       2622 ns     265024  
BMCatmullRomConstructor<double>/512         4967 ns       4944 ns     139068  
BMCatmullRomConstructor<double>/1024       10677 ns      10624 ns      66070  
BMCatmullRomConstructor<double>/2048       20435 ns      20415 ns      33994  
BMCatmullRomConstructor<double>/4096       43460 ns      43127 ns      15953  
BMCatmullRomConstructor<double>/8192       91810 ns      91277 ns       6671  
BMCatmullRomConstructor<double>/16384     187199 ns     186438 ns       3659  
BMCatmullRomConstructor<double>/32768     375349 ns     373018 ns       1784  
BMCatmullRomConstructor<double>_BigO       11.42 N      11.36 N  
BMCatmullRomConstructor<double>_RMS            3 %          3 %  
       24.27 real        24.01 user         0.12 sys  
```  
  
A little more than double.  
  
Code:  
  
```cpp  
#include <benchmark/benchmark.h>  
#include <array>  
#include <iostream>  
#include <boost/math/interpolators/catmull_rom.hpp>  
  
using boost::math::catmull_rom;  
template <class Real>  
void BMCatmullRomConstructor(benchmark::State& state)  
{  
    for (auto _ : state)  
    {  
        std::array<Real, 3> p{1.0,2.0,3.0};  
        std::vector<std::array<Real, 3>> v(state.range(0));  
        for (size_t i = 0; i < v.size(); ++i)  
        {  
            v[i] = p;  
            p[0] += 1;  
        }  
  
        catmull_rom h(std::move(v));  
        benchmark::DoNotOptimize(h.max_parameter());  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK_TEMPLATE(BMCatmullRomConstructor, float)->RangeMultiplier(2)->Range(1<<2, 1<<15)->Complexity(benchmark::oN);  
BENCHMARK_TEMPLATE(BMCatmullRomConstructor, double)->RangeMultiplier(2)->Range(1<<2, 1<<15)->Complexity(benchmark::oN);  
  
BENCHMARK_MAIN();  
```

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2018-12-03 19:59:17 UTC  
> Url: https://github.com/boostorg/math/pull/95#issuecomment-443849186  

>Yes, but the beauty of using compactly supported basis functions is that the weird behavior is confined to single segments. As to always using the last point as the control for the first segment, well, the traditional alternative is using zero, which breaks affine invariance. There are really no great options here; maybe one of the best options is to recommend to the user that they define "reasonable control points" as the first and last points.  
  
IMO as log as the weirdness is confined to those segments, and the user is made aware it can happen that's fine.  
  
>The user can certainly do that. Another option is that we add two more points to the user's data: For example, form a line between the first and second points, and place our first point along the line before the first point. Then do the same for the last point. Then the whole issue is resolved, but maybe not resolved in a way people expect, so you'd have to make it an option. But for some reason, that feels like optionitis, not design.  
  
+1 on optionitis.  
  
Might be worth mentioning that this is something the user can choose to do if they know where the outside control points should be?  
  
>Will an "average" use fit into a stack allocation? That would be amazing, but it's unclear to me that it will be sufficiently common that it should be supported. In any case, if we templated on a RandomAccessContainer, then the call to resize() in the constructor could mean "add more stack space".  
  
I'm not sure about "average", depends how many control points are typical - the examples I've seen all only have a tiny number of points, but I'm assuming that's not typical?  In any case adding a second template parameter for storage would be backwards compatible so we could just wait and see.

---

## Comment 11

> Username: NAThompson  
> Created_at: 2018-12-03 20:14:38 UTC  
> Url: https://github.com/boostorg/math/pull/95#issuecomment-443854057  

> depends how many control points are typical - the examples I've seen all only have a tiny number of points, but I'm assuming that's not typical?  
  
My vision is that this will be used in SVG-2 to plot time series data, see [here](https://www.w3.org/TR/svg-paths/#PathDataCatmullRomCommand). Looking at d3.js, it seems like the amount of data passed into the Catmull-Rom curve will be quite large on average. Of course, predicting how people will actually use the thing, or even will use it at all, is impossible.  
  
> Might be worth mentioning that this is something the user can choose to do if they know where the outside control points should be?  
  
[This work?](https://github.com/boostorg/math/pull/95/commits/081842118bd4cf270b48c06fb66db85d400d3c42)

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2018-12-03 20:39:39 UTC  
> Url: https://github.com/boostorg/math/pull/95#issuecomment-443861841  

>This work?  
  
Yup :)

---

## Comment 13

> Username: NAThompson  
> Created_at: 2018-12-04 17:38:46 UTC  
> Url: https://github.com/boostorg/math/pull/95#issuecomment-444189090  

Just so I can remember this in the future: There is a chance that the log(N) evaluation can be improved upon. One way is to do interpolation search, rather than binary search (see [here](http://www.keithschwarz.com/interesting/code/?dir=interpolation-search)). Assuming that the distance between points is uniform, this would make the evaluation O(log(log(N)). Unfortunately, it makes the worst case evaluation linear in N, so whether or not this actually improves speed in practice depends on what "typical" data is.  
  
The next method is to store the iterator that was found during the last evaluation. If the user increases the parameter between each call, then the evaluation time would be constant, and would decay to log(N) for random parameter evaluation. This would remove threadsafety, which might not be that big of a deal. Unfortunately, there's another problem, which is that the logic gets more complicated. The current implementation requires between 20-50ns per call (depending on the size of the input vector), so there's actually not that much leeway to add logic without slowing the routine down. I implemented this idea and sadly obtained no speedup, but I'm not yet giving up on the idea.

---
