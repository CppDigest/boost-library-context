# #743 Refactor for_each_x and implement new algorithms. [Merged]

> Username: awulkiew  
> Created at: 2020-08-10 18:48:16 UTC  
> Updated at: 2020-08-24 10:37:24 UTC  
> Merged at: 2020-08-24 10:37:24 UTC  
> Closed at: 2020-08-24 10:37:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/743  

I was playing with for_each family of functions and C++14. With the newer standard we can greatly simplify some parts of the code applying some Actor/Policy to Points/Segments/Ranges or checking some predicate for them.  
Let me know what you think.  
  
Changes summary:  
  
`for_each_range` now expects functors instead of structs with `apply()` member function.  
  
`for_each_segment` now calls functor also for degenerated ranges for consistency with `segment_iterator`.  
  
New algorithms based on `std::all_of`, `std::any_of` and `std::none_of`  
- `all_points_of`, `any_point_of`, `none_point_of`  
- `all_segments_of`, `any_segment_of`, `none_segment_of`  
- `all_ranges_of`, `any_range_of`, `none_range_of`  
  
New algorithms used in several places in the library.

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2020-08-11 10:19:56 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/743#pullrequestreview-464926433  

I took the liberty to have a look, and it LGTM.  
  
I just left some questions to educate myself mostly.

📁 .github/workflows/minimal-clang.yml

```diff
  35 |-             b2_cxxstd: 03,11
  35 |+ #            b2_cxxstd: 03,11
  36 |+             b2_cxxstd: 14
```

> Username: mloskot  
> Created_at: 2020-08-11 10:06:41 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r468469886  

I'd simply remove the commented lines to keep the YAML-s compact, easier to maintain.

> Username: awulkiew  
> Created_at: 2020-08-11 14:47:25 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r468639264  

Done.


📁 include/boost/geometry/algorithms/detail/disjoint/areal_areal.hpp

```diff
 147 |-                                 strategy.get_disjoint_segment_box_strategy()) )
 119 |+         auto ds = strategy.get_disjoint_segment_box_strategy();
 120 |+         if (! geometry::all_segments_of(areal, [&](auto const& s)
```

> Username: mloskot  
> Created_at: 2020-08-11 10:10:52 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r468472002  

Nitpicks:  
- could be `auto const ds` as it's passed to `apply` via reference to `const`, AFAIR.  
- capture `[&]` could be `[&box, &ds]` for clarity and, theoretically as I have not checeked, optimisation

> Username: awulkiew  
> Created_at: 2020-08-11 10:44:01 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r468488853  

I'll add `const`.  
  
I used `&` to have the line as short as possible. Do you think lambdas automatically store references to all local variables?

> Username: mloskot  
> Created_at: 2020-08-11 10:49:59 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r468491788  

The lazy `[&]` captures everything by reference and the compiler should be clever enough to optimise away anything that is not explicitly referenced inside the lambda.  
  
I aimed to provoke some thoughts on which one is clearer for a reader, but yeah, shorter seems better.

> Username: awulkiew  
> Created_at: 2020-08-11 11:04:41 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r468499123  

I don't have an opinion about it actually. If the compiler captures everything then maybe it'd be a good idea to explicitly limit it and not use the default captures at all. This would be a premature optimization though. This needs more research.

> Username: mloskot  
> Created_at: 2020-08-11 11:11:51 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r468502401  

I'm sure things are optimised away in this case.  
My suggestion was of the same category as declare/include what you use, declare variables in as narrow scope as possible.  
Anyway, thanks for comments, it's not an issue that needs any fixing for me.


📁 include/boost/geometry/algorithms/detail/for_each_range.hpp

```diff
 191 |-         >::apply(geometry, actor);
 247 |+     return ! dispatch::for_each_range<Geometry const>::apply(geometry,
 248 |+                 [&](auto&& range)
```

> Username: mloskot  
> Created_at: 2020-08-11 10:12:30 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r468472748  

The `[&]` captures only `p`, doesn't it? If so, could be limited too.


📁 include/boost/geometry/algorithms/detail/touches/implementation.hpp

```diff
 207 |+     typename geometry::point_type<Geometry1>::type pt;
 208 |+     return geometry::point_on_border(pt, geometry1)
 209 |+         && geometry::within(pt, geometry2, strategy);
```

> Username: mloskot  
> Created_at: 2020-08-11 10:15:16 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r468474078  

A theoretical question, does anyone know if optimisers can handle this old-fashioned piece better?  
  
```cpp  
if  (geometry::point_on_border(pt, geometry1)) // pt is output  
     return geometry::within(pt, geometry2, strategy); // pt is input  
else  
    return false;  
```  
  
I remember old compilers/optimisers tripping on some uses of ternary operator :)

> Username: awulkiew  
> Created_at: 2020-08-11 11:00:30 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r468497054  

I don't know.  
  
So the version with ternary operator would be yet another one:  
```  
return geometry::point_on_border(pt, geometry1)  
     ? geometry::within(pt, geometry2, strategy)  
     : false;  
```  
  
I always assumed the compiler can do whatever it wants in any case and if we wanted to be sure we would have to check each version of each one of them (a good example is `switch` and which `case` is checked first, etc.). So I tend to use logical `&&` to check conditions one by one because it is the shortest.

> Username: mloskot  
> Created_at: 2020-08-11 11:04:02 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r468498811  

> I tend to use logical && to check conditions one by one because it is the shortest.  
  
Yes, makes sense, shorter seems better again.  
I'm too lazy to check the Compiler Explorer output right now.  
  
>  version with ternary operator would be yet another one  
  
Generally, I try to avoid the ternary operator. I'd rather use the long if-else instead.


📁 include/boost/geometry/algorithms/for_each.hpp

```diff
 234 |         *it0 = p0;
 235 |         *it1 = p1;
 236 |+         return result;
```

> Username: mloskot  
> Created_at: 2020-08-11 10:19:04 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r468476056  

There is a reason why `f(s)` is called (and returned boolean cached) before `p0` and `p1` are accessed, right?  
  
Shouldn't the assignments be skipped all together if the `f` fails?  
  
```cpp  
if (!f(s))  
    return false;  
  
*it0 = p0;  
*it1 = p1;  
return true;  
```

> Username: awulkiew  
> Created_at: 2020-08-11 10:53:31 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r468493556  

I agree with you in general however my intention was to generate exactly the same outcome as in case of the version for lvalue mutable iterators. The caller doesn't know whether or not the points are going to be modified inside `f`. The points can be modified and the functor can still return `false`. The final assignment would reflect that, i.e. this code would work exactly how it works for real references.  
  
An example would be an algorithm transforming N points of a geometry or something and stopping as soon as they are transformed. I don't know if this algorithm makes sense but I'm just showing a scenario when the outcomes would be different.  
  
Or am I missing something?

> Username: mloskot  
> Created_at: 2020-08-11 10:59:27 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r468496499  

> The points can be modified and the functor can still return false.  
  
It is an important clarification.  
It makes the difference to the approach which I assumed, i.e. in-out arguments remain untouched on failure.  
  
My questions have been answered, thanks.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2020-08-11 15:12:44 UTC  
> Updated_at: 2020-08-13 15:18:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#issuecomment-672006278  

Thanks for the review @mloskot .  
  
I was searching for a candidate for refactoring using these functions. `length()` seems to be the most straightforward one. For all linear geometries it could be implemented as something like this:  
```  
for_each_segment(linear, [&](auto const& s){  
    strategy.apply(make_indexed_view<0>(s), make_indexed_view<1>(s));  
});  
```  
  
We could also have a version of `for_each_segment` (with a different name) taking a functor taking 2 points instead of a segment which would make this even simpler:  
```  
for_each_something(linear, [&](auto const& p0, auto const& p1){  
    strategy.apply(p0, p1);  
});  
```  
  
`transform()` could nearly be implemented with `all_points_of()` in its entirety but the problem is that it resizes the geometries in the process so we'd need a different algorithm for that. ~An algorithm which maybe could also be used for `simplify()`~.

---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2020-08-19 08:05:40 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/743#pullrequestreview-470214969  

Thanks, looks good.

📁 include/boost/geometry/strategies/agnostic/hull_graham_andrew.hpp

```diff
 232 | 
 233 |         //std::cout << boost::size(assigner.lower_points) << std::endl;
 234 |         //std::cout << boost::size(assigner.upper_points) << std::endl;
```

> Username: barendgehrels  
> Created_at: 2020-08-19 07:59:50 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r472831383  

we can remove this, since we're reworking this now

---

📁 include/boost/geometry/strategies/agnostic/hull_graham_andrew.hpp

```diff
 155 |-             it != boost::end(range);
 156 |-             ++it)
 127 |+         for (auto it = boost::begin(range); it != boost::end(range); ++it)
```

> Username: barendgehrels  
> Created_at: 2020-08-19 08:04:28 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r472835442  

Also here a range based loop instead?

> Username: awulkiew  
> Created_at: 2020-08-19 20:51:21 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r473311004  

Same here, we require `Range` adapted to Boost.Range concept which is not required to work with range-based for loops.


📁 include/boost/geometry/algorithms/detail/for_each_range.hpp

```diff
  78 |-                 it = boost::begin(multi); it != boost::end(multi); ++it)
 114 |+         auto const end = boost::end(multi);
 115 |+         for (auto it = boost::begin(multi); it != end; ++it)
```

> Username: barendgehrels  
> Created_at: 2020-08-19 08:02:50 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r472834068  

Since you're reworking this, it's nicer to use a range based loop here  
`for (auto& geometry : multi)` {... `apply(geometry, f)` }

> Username: awulkiew  
> Created_at: 2020-08-19 20:49:15 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r473309907  

This is probably not possible. It's because we require `MultiGeometry` to be adapted to Boost.Range concept. So it's possible to adapt it and at the same time avoid implementing `begin` and `end` members or free functions that could be found via ADL. Consider e.g. `std::pair<T*, T*>` which is adapted to Boost.Range concept but can't be used in range-based for loop.  
  
Unless I'm missing something and e.g. Boost.Range defines some free functions enabled conditionally for adapted types. I quickly tried to find something like this but was unable to do it. Plus I'm not sure if they would implement something like this because it's not clear to me that this would not cause any problems.

> Username: awulkiew  
> Created_at: 2020-08-19 23:34:15 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r473435487  

Actually we could replace it with something like:  
```  
for (auto&& geometry : boost::make_iterator_range(multi)) {  
// ...  
}  
```  
or  
```  
boost::for_each(multi, [&](auto&& geometry){  
// ...  
});  
```  
but I think I'd prefer to keep the algorithm as simple as possible and not create unnecessary objects.

> Username: barendgehrels  
> Created_at: 2020-08-22 09:08:06 UTC  
> Updated_at: 2020-08-24 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/743#discussion_r475066572  

Yes, it's OK as it is, I just thought it could be done here, but if it is not, let's not do it indeed.  
  
Thanks for your answers.


---
