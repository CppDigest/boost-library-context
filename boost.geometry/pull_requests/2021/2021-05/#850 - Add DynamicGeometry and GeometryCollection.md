# #850 Add DynamicGeometry and GeometryCollection. [Merged]

> Username: awulkiew  
> Created at: 2021-05-17 21:35:55 UTC  
> Updated at: 2021-06-03 14:14:51 UTC  
> Merged at: 2021-06-03 14:14:51 UTC  
> Closed at: 2021-06-03 14:14:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/850  

After: https://github.com/boostorg/geometry/issues/843  
  
This PR adds DynamicGeometry and GeometryCollection and usage in several algorithms.  
  
List of changes:  
- Added DynamicGeometry concept  
  - Required specialization of `traits::tag<>` defining `type` as `dynamic_geometry_tag`  
  - Required specialization of 1-parameter `traits::visit<>`  
  - Required specialization of `traits::geometry_types<>`  
  - Optional specialization of 2-parameter `traits::visit<>`  
  - [mutable] Must be assignable from types specified in `traits::geometry_types<>`  
- Added GeometryCollection concept  
  - Required specialization of `traits::tag<>` defining `type` as `geometry_collection_tag`  
  - Required adaptation to ForwardRange concept  
  - Optional specialization of `traits::geometry_types<>` (by default using `traits::geometry_types<>` for `range_value<>::type`)  
  - Optional specialization of `traits::iter_visit<>` (by default calling `traits::visit<>` for `range_value<>::type`)  
  - [mutable] Required specialization of `traits::emplace_back<>` allowing to add objects of types specified in `traits::geometry_types<>`  
  - [mutable] Optional specialization of `traits::clear<>` (by default calling `collection.clear()`)  
  - May be recursive  
- Added adaptations of DynamicGeometry  
  - `boost::variant<>`  
  - `boost::variant2::variant<>`  
  - `boost::any<>` (requires additional user specialization of `traits::geometry_types<>`)  
  - `std::variant<>`  
  - `std::any<>` (requires additional user specialization of `traits::geometry_types<>`)  
- Added GeometryCollection models  
  - `model::geometry_collection<>`  
- Added concept checks for DynamicGeometry and GeometryCollection  
  - added new util `concepts::concept_type<>` defining concept of Geometry in a generic way  
  - refactored `concepts::check()`, dispatches replaced with `concepts::concept_type<>`  
- Added algorithms:  
  - `detail::visit()`  
  - `detail::visit_breadth_first()`  
- Added support for DynamicGeometry and GeometryCollection in:  
  - `append()` (only DynamicGeometry)  
  - `area()`  
  - `clear()`  
  - `disjoint()` (the algorithm for 2 GeometryCollections has quadratic complexity so should probably be refactored)  
  - `length()`  
  - `read_wkt()`  
  - `wkt()`  
  - `comparable_distance_result<>`  
  - `distance_result<>`  
- Other  
  - Added support for rvalue references to range `value_type` in `traits::push_back` and `range::push_back` to support move semantics (the support for rvalue reference for the Range itself should still be added in a different PR)  
  - `boost::variant` workarounds previously implemented for VS2015 now works with all compilers `#ifdef BOOST_VARIANT_DO_NOT_USE_VARIADIC_TEMPLATES` (in various algorithms)  
  
I'm open to suggestions.

---

## Review 1 [Commented]

> Username: mloskot  
> Created_at: 2021-05-24 21:16:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/850#pullrequestreview-667174096  

📁 include/boost/geometry/algorithms/detail/disjoint/interface.hpp

```diff
 110 |+     bool IsDynamic = util::is_dynamic_geometry<Geometry1>::value
 111 |+                   || util::is_dynamic_geometry<Geometry2>::value,
 112 |+     bool IsGC = util::is_geometry_collection<Geometry1>::value
```

> Username: mloskot  
> Created_at: 2021-05-24 21:16:43 UTC  
> Updated_at: 2021-05-24 21:16:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r638275507  

Nitpick: since there is non-abbreviated `IsDynamic`, then `IsGC` could be `IsCollection` :)


---

## Review 2 [Commented]

> Username: mloskot  
> Created_at: 2021-05-24 21:25:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/850#pullrequestreview-667196451  

📁 include/boost/geometry/algorithms/visit.hpp

```diff
 239 |+         std::enable_if_t<util::is_geometry_collection<Geom>::value, int> = 0
 240 |+     >
 241 |+     static void set_iterators(Geom & g, Iterator & first, Iterator & last)
```

> Username: mloskot  
> Created_at: 2021-05-24 21:25:59 UTC  
> Updated_at: 2021-05-24 21:26:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r638279607  

I guess it could not be `Geom const& g`

> Username: awulkiew  
> Created_at: 2021-05-24 22:38:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r638334390  

The point is to support both const and non-const references here. This is also why a function template argument is used instead of struct template argument `Geometry`.


---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2021-05-24 21:29:16 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/850#pullrequestreview-667205486  

I've skimmed the code trying to understand the core parts and nothing looks suspicious.  
Nice feature!

---

## Comment 4

> Username: awulkiew  
> Created_at: 2021-05-24 22:51:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#issuecomment-847402477  

@mloskot Thanks for the review!  
  
The thing that I don't like about it is the fact that `traits::visit` has to be specialized for a specific number of geometries, one or two. Do you have a different idea how this trait could be implemented to allow specialization for e.g. any number of arbitrary variants (potentially holding different geometries)?  
  
Do you think that rvalue references should be handled in visit too?  
  
Do you think visit should be able to return a value?

---

## Comment 5

> Username: mloskot  
> Created_at: 2021-05-25 06:28:02 UTC  
> Updated_at: 2021-05-25 06:28:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#issuecomment-847579043  

> Do you have a different idea how this trait could be implemented to allow specialization for e.g. any number of arbitrary variants  
  
My C++ is getting rusty, but it generally should be feasible (https://stackoverflow.com/q/24687026/151641 or https://bitbashing.io/std-visit.html), but I'm sure you've looked at the variadics.  
  
> Do you think that rvalue references should be handled in visit too?  
> Do you think visit should be able to return a value?  
  
I think it is generally a good idea to keep interfaces aligned with the standard stuff, i.e. similar to `std::visit`  
  
I wish I could be more helpful, but can't afford any experiments on my own right now.

---

## Review 6 [Changes requested]

> Username: barendgehrels  
> Created_at: 2021-05-26 08:54:06 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/850#pullrequestreview-668735452  

Thanks a lot!  
  
I think at least some of the `TODO`'s should be done, or rephrased, and some commented code can go or be uncommented, so I set this to `Request changes` for now, but apart from that, I'm fine.

📁 include/boost/geometry/algorithms/append.hpp

```diff
 165 |+ struct append
 166 |+     : detail::append::append_no_action
 167 | {};
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:26:07 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639509832  

Looks fine, thanks for the clean up

---

📁 include/boost/geometry/algorithms/append.hpp

```diff
 345 |-             variant_geometry
 346 |-         );
 240 |+             // TODO: move this to bg::append()?
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:27:08 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639510553  

Probably, yes, we usually don't do this in the dispatch

> Username: awulkiew  
> Created_at: 2021-05-26 14:13:21 UTC  
> Updated_at: 2021-05-28 14:16:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639767613  

Actually we do this in other functions too (e.g. in `intersection`). Right now it has to be done after the `resolve_variant` stage because only at this point we know what is the type of the actual geometry. So the question is, do we keep the old behavior or do we implement `check` for DynamicGeometry checking all of the geometries stored in it before, at the algorithm stage? I don't have an opinion about it. We could as well leave it as it is since the dispatching has to be done anyway here.  
  
EDIT: There are additional checks that could be performed for DynamicGeometry and GeometryCollection. E.g. DynamicGeometry has to be assignable from any of the Geometries that can be stored and GeometryCollection has to be a range. So this could be checked in concept check and checks for specific geometries could be done later after the `resolve_variant` stage, so in both places.

> Username: barendgehrels  
> Created_at: 2021-06-02 09:58:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r643807267  

Thanks! I'm fine checking it in dispatch too.  
It's mainly about the error message: is it clear to the user if it is still called from dispatch?  
But the message will change in C++20 anyway, if we replace BOOST_CONCEPT with the standard concepts, so I'm fine either way.


📁 include/boost/geometry/algorithms/area.hpp

```diff
 304 |-         typename area_result<variant_type, Strategy>::type
 305 |-         operator()(Geometry const& geometry) const
 296 |+         // TODO: Technically value could be returned directly from visit
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:28:57 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639512007  

Current version looks fine to me and consistent with the version below

> Username: awulkiew  
> Created_at: 2021-05-26 14:25:27 UTC  
> Updated_at: 2021-05-26 14:25:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639779013  

Plus there is a problem with the old `variant` which forces us to define the return type as the template parameter of `boost::static_visitor<>`. I tried to define it automatically based on the return type of the functor for the first type stored in a variant and it would be doable but the adaptation of `boost::variant` would be a lot more complex. We would be able to write:  
```  
    return visit<...>(...);  
```  
but I think in general `area_result<>` would still be needed so I doubt it'd be worth it to make visit more complex only to be able to ommit two lines in some places of the code. I only worry about cutting of the possibility because it's possible that in the (most likely distant) future we could need this for something. But that's only that.


📁 include/boost/geometry/algorithms/detail/disjoint/interface.hpp

```diff
 170 |+                     >::apply(g1, g2, strategy);
 171 |+                 // If any of the combination intersects then the final result is not disjoint
 172 |+                 //   so break testing.
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:32:48 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639514934  

I think the last part `so break testing` can be omitted

---

📁 include/boost/geometry/algorithms/detail/disjoint/interface.hpp

```diff
 175 |+             return result;
 176 |+         }, geometry1);
 177 |+         return result;
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:33:49 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639515659  

I don't get the logic yet but I didn't dive into `visit_breadth_first` yet

> Username: awulkiew  
> Created_at: 2021-05-26 14:30:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639784017  

It's based on the assumption that geometry collections can be recursive. So a GC can store GCs. AFAIU this can be the case. So this function traverses the recursive GC in a breadth-first fashion (as opposed to depth-first). And as long as the function/lambda returns `true` the traversal is continued. Maybe I should've called it differently. E.g. `visit_breadth_first_if`?  
  
So here as long as pairs of geometries are keep being disjoint then the traversal should continue. Hence the `result` is returned because it's `true` if pairs of geometries are being disjoint.  
  
Or is it not clear from a different reason?

> Username: barendgehrels  
> Created_at: 2021-06-02 11:08:21 UTC  
> Updated_at: 2021-06-02 11:08:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r643855273  

It's clear now. Thank you.


📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 296 |             {
 297 |-                 range::push_back(interior_rings_out, out);
 297 |+                 range::push_back(interior_rings_out, std::move(out));
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:35:06 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639516596  

`move` is better, thanks


📁 include/boost/geometry/algorithms/visit.hpp

```diff
 193 |+                 }, it);
 194 |+                 if (! result)
 195 |+                     return false;
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:36:51 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639517854  

Curly braces are missing

> Username: awulkiew  
> Created_at: 2021-05-26 15:03:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639816158  

What do you think about relaxing this guideline? I.e. to allow single lines under if-else? From my point of view adding curly braces around this one line makes the whole algorithm less readable because there are 2x as much lines of code that can be seen by the eye, the information is the same and this space could be used for other things.  
  
It'd be a different thing if this was very long line which potentially could be wrapped into yet another line. In this case I'd argue that curly braces are indeed desireable. But in case of a short call or return statement. I don't think that it is.  
  
Do you strongly feel that these braces are necessary here?

> Username: awulkiew  
> Created_at: 2021-06-02 12:11:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r643898782  

I added them but I'll probably create an issue to discuss this later.

---

📁 include/boost/geometry/algorithms/visit.hpp

```diff
 247 |+     <
 248 |+         typename Geom, typename Iterator,
 249 |+         std::enable_if_t<!util::is_geometry_collection<Geom>::value, int> = 0
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:38:25 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639519018  

Minor, space after `!` is missing

---

📁 include/boost/geometry/algorithms/visit.hpp

```diff
 190 |+                 traits::visit_iterator<util::remove_cref_t<Geom>>::apply([&](auto & g)
 191 |+                 {
 192 |+                     result = visit_or_enqueue(function, g, queue, it);
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:40:50 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639520817  

OK, I see, it visits all elements in the collection without recursion.  
I'm not a fan of storing iterators but I think it's OK here.

> Username: awulkiew  
> Created_at: 2021-05-26 15:07:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639819617  

Do you mean you don't like dealing with recursion this way (i.e. using manual stack or queue) and prefer recursion using function stack?  
Or would you prefer storing e.g. pointers or something else?

---

📁 include/boost/geometry/algorithms/visit.hpp

```diff
 297 |+ // TODO: We could change the order of the arguments but I'm not sure if that's a good idea.
 298 |+ template <typename UnaryFunction, typename Geometry>
 299 |+ inline void visit_breadth_first(UnaryFunction function, Geometry & geometry)
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:42:15 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639521898  

Is this a function for the end user?  
If so we should resolve all todo's and make a decision about the order.  
Or should we move it to `namespace detail` first?

> Username: barendgehrels  
> Created_at: 2021-05-26 08:43:17 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639522620  

Same for the `visit` functions above

> Username: awulkiew  
> Created_at: 2021-05-26 15:15:18 UTC  
> Updated_at: 2021-05-26 15:15:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639826988  

Yes, moving it to `detail` is a good idea regardless of the order of arguments.  
  
Regarding the order, it depends if we think it's more like `std::visit` or `std::for_each`. I'm leaning more towards having the same interface for the whole family of `visit` functions also consistent with `std::visit`.

> Username: awulkiew  
> Created_at: 2021-05-28 00:01:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r641059816  

I moved them to `detail`.


📁 include/boost/geometry/core/mutable_range.hpp

```diff
 115 |+         <
 116 |+             typename std::remove_reference<Range>::type
 117 |+         >::type size_type;
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:44:25 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639523503  

New types should actually be defined by `using`

---

📁 include/boost/geometry/core/mutable_range.hpp

```diff
 101 |+     {
 102 |+         range.emplace_back(std::forward<Args>(args)...);
 103 |+     }
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:45:02 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639524026  

This will break older code? (with own types)? Or is it optional?

> Username: awulkiew  
> Created_at: 2021-05-26 15:25:26 UTC  
> Updated_at: 2021-05-26 15:25:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639836332  

Not by itself. It could break the code if we replaced `push_back` with it and a user-defined geometry wouldn't also define `emplace_back`.  
  
This is why `emplace_back` should be used only with GCs. Why not `push_back`? Because a GC can store a different type than the one that is passed. E.g. variant is stored but we're emplacing back a point, or GC stores pointers to geometries but we pass a linestring.  
  
For all other geometries we know the type that is stored and we should keep using `push_back`. However now we can also move which will perform copy if the user-defined geometry doesn't define move ctor.


📁 include/boost/geometry/extensions/gis/io/shapefile/read.hpp

```diff
 623 |             else
 628 |-                 range::push_back(inner_rings, ring);
 624 |+                 range::push_back(inner_rings, std::move(ring));
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:46:15 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639524984  

curly braces are missing (was already)


📁 include/boost/geometry/geometries/adapted/boost_any.hpp

```diff
  14 |+ #include <utility>
  15 |+ 
  16 |+ #include <boost/any.hpp>
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:47:26 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639525861  

Do we still need it? `std::any` will be there in C++17  
But I'm fine with this one too

> Username: awulkiew  
> Created_at: 2021-05-26 15:29:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639839530  

I don't know. BG is a C++14 library, so I guess? I implemented it more like a proof of concept because I doubt that anyone will use `boost::any` with BG since there is `variant2` but who knows. In general no adaptations are needed becasue a user could write them.

> Username: awulkiew  
> Created_at: 2021-05-31 17:56:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r642621441  

With that said I think it'd better that we do this both for user convenience and to make sure the the adaptations are correct.

> Username: barendgehrels  
> Created_at: 2021-06-02 09:59:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r643808275  

Thank you, it's fine to keep them and be sure about it. It was just a consideration.


📁 include/boost/geometry/geometries/adapted/boost_variant.hpp

```diff
  22 |+ #include <boost/variant/apply_visitor.hpp>
  23 |+ #include <boost/variant/static_visitor.hpp>
  24 |+ #include <boost/variant/variant.hpp>
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:48:29 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639526766  

Same here, `std::variant` in C++17

> Username: awulkiew  
> Created_at: 2021-05-26 15:31:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639842609  

The adaptation for `boost::variant` is for backward compatibility.  
For `boost::variant2::variant` is there because BG is C++14 library.  
  
And yes, the adaptation for `std::any` and `std::variant` should be added as well. But then this PR would be even bigger.

> Username: awulkiew  
> Created_at: 2021-05-26 15:33:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639845679  

The adaptation for `boost::variant` is also included by default by the algorithms for backward compatibility. We could change that forcing the users to include this optional file but technically this would be a breaking change.

> Username: awulkiew  
> Created_at: 2021-05-28 17:07:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r641695255  

I added adaptations for `std::any` and `std::variant`.


📁 include/boost/geometry/geometries/concepts/check.hpp

```diff
 184 |+ template <typename Geometry, bool IsConst>
 185 |+ struct check<Geometry, dynamic_geometry_tag, IsConst>
 186 |+     // TODO traits::visit
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:49:28 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639527507  

There are still many `TODO` 's in this PR.  
I don't know how important they are, and when this should be done.

> Username: awulkiew  
> Created_at: 2021-05-26 15:40:44 UTC  
> Updated_at: 2021-05-26 15:40:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639856609  

There are many TODOs because there are many ways how to proceed. We also didn't talk about it on the Issues page. So I'm treating this PR as a ground for brainstorming.  
  
The question here is: should we check all of the geometries stored in a dynamic geometry here or rather do this in a `variant_dispatch` (see above) and here ignore the check?  
  
Btw, the `traits::visit` is wrong. It'd have to be a`concept_check` for `concepts::DynamicGeometry` checking all of the types defined in `traits::geometry_types<>::type`.

> Username: awulkiew  
> Created_at: 2021-05-31 17:54:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r642620682  

There are the concepts for DG and GC now, though only the first geometry from the `geometry_types<>` sequence is checked there because we also need it to check whether or not it can be assigned to DG or emplaced_back to GC.  
  
So I see two possibilities, as I mentioned:  
- check the first type in the concept check for DG and GC and the rest when they are actually used, i.e. in the `variant_dispatch` portion of an algorithm  
- check all of the types in the concept check  
  
Do you have any preference?

> Username: barendgehrels  
> Created_at: 2021-06-02 10:03:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r643810848  

As mentioned above, I'm fine either way. If I would have a preference, I would select the option which gives the most clear error message if users pass a wrong (not fulfilling) geometry. Unless that requires a lot of extra code.

> Username: awulkiew  
> Created_at: 2021-06-02 10:42:44 UTC  
> Updated_at: 2021-06-02 10:47:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r643838378  

It wouldn't require a lot of extra code. I'm not sure about the error message, it'd probably be the same but I think I'll do this in concepts to keep all of the checks in one place.

> Username: barendgehrels  
> Created_at: 2021-06-02 11:07:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r643854695  

OK

> Username: awulkiew  
> Created_at: 2021-06-02 20:51:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644311535  

Done.


📁 include/boost/geometry/geometries/geometry_collection.hpp

```diff
  46 |+ //    geometry_collection(Iterator begin, Iterator end)
  47 |+ //        : base_type(begin, end)
  48 |+ //    {}
```

> Username: barendgehrels  
> Created_at: 2021-05-26 08:50:02 UTC  
> Updated_at: 2021-05-26 08:54:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639527922  

Delete this?

> Username: awulkiew  
> Created_at: 2021-05-26 15:35:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r639848534  

Or add ctors in all models (linestring, ring, etc.) taking a pair of iterators?

> Username: barendgehrels  
> Created_at: 2021-06-02 10:04:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r643811349  

We might do that, but as far as I know, no user asked for this.

> Username: awulkiew  
> Created_at: 2021-06-02 10:37:01 UTC  
> Updated_at: 2021-06-02 10:37:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r643834622  

No user asked for the `initializer_list` either. This is just something I found useful at some point during testing. But I'm ok with leaving it as it is.

> Username: barendgehrels  
> Created_at: 2021-06-02 11:07:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r643855001  

OK (we can always add it later if necessary)


---

## Comment 7

> Username: awulkiew  
> Created_at: 2021-05-28 17:13:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#issuecomment-850557063  

@mloskot I added support for rvalue references because it is possible we'll want to move something inside visit. I didn't implement returning values though because:  
- in some cases the implementation would be complicated and still not 100% bulletproof, e.g. for `boost::variant` forcing to define return value as template argument of `boost::static_visitor<>`,  
- this would be a requirement for the users adapting their classes and this requirement would make this more complicated as well.  
  
So I don't think it's worth it only to be able to save 2 lines of code now and then.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2021-05-31 17:46:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#issuecomment-851614708  

@barendgehrels I made the PR more complete. I added the list of changes in the description.

---

## Review 9 [Approved]

> Username: barendgehrels  
> Created_at: 2021-06-02 10:42:33 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/850#pullrequestreview-674001338  

Thanks!

📁 include/boost/geometry/geometries/adapted/std_any.hpp

```diff
  14 |+ #include <boost/config.hpp>
  15 |+ 
  16 |+ #ifndef BOOST_NO_CXX17_HDR_ANY
```

> Username: barendgehrels  
> Created_at: 2021-06-02 10:06:29 UTC  
> Updated_at: 2021-06-02 10:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r643813242  

This looks good, thanks.


📁 include/boost/geometry/geometries/adapted/boost_variant2.hpp

```diff
  32 |+ struct tag<boost::variant2::variant<Ts...>>
  33 |+ {
  34 |+     typedef dynamic_geometry_tag type;
```

> Username: barendgehrels  
> Created_at: 2021-06-02 10:07:45 UTC  
> Updated_at: 2021-06-02 10:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r643814211  

Suggestion: `using type = ...`


---

## Review 10 [Approved]

> Username: vissarion  
> Created_at: 2021-06-03 10:11:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/850#pullrequestreview-675007882  

Thanks and sorry for late reviewing, I am OK for merging.

📁 include/boost/geometry/algorithms/area_result.hpp

```diff
  73 | {};
  74 | 
  77 |- // Workaround for VS2015
```

> Username: vissarion  
> Created_at: 2021-06-03 08:21:23 UTC  
> Updated_at: 2021-06-03 10:11:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644588872  

is not needed anymore?

> Username: awulkiew  
> Created_at: 2021-06-03 10:20:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644673082  

No, the workaround was replaced by proper handling of `#ifdef BOOST_VARIANT_DO_NOT_USE_VARIADIC_TEMPLATES` in `geometries/adapted/boost_variant.hpp`


📁 include/boost/geometry/algorithms/append.hpp

```diff
  82 |-              it != boost::end(range);
  83 |-              ++it)
  76 |+         typedef typename boost::range_value<Range>::type point_type;
```

> Username: vissarion  
> Created_at: 2021-06-03 08:26:41 UTC  
> Updated_at: 2021-06-03 10:11:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644592664  

since you are changing it, why not with `using` ?

---

📁 include/boost/geometry/algorithms/append.hpp

```diff
  91 |+                              signed_size_type ring_index, signed_size_type = 0)
  92 |     {
  93 |+         typedef typename ring_type<Polygon>::type ring_type;
```

> Username: vissarion  
> Created_at: 2021-06-03 08:49:29 UTC  
> Updated_at: 2021-06-03 10:11:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644609537  

`using` instead of `typedef`?

---

📁 include/boost/geometry/algorithms/append.hpp

```diff
 115 |+                              signed_size_type ring_index, signed_size_type = 0)
 116 |     {
 117 |+         typedef typename ring_type<Polygon>::type ring_type;
```

> Username: vissarion  
> Created_at: 2021-06-03 08:49:50 UTC  
> Updated_at: 2021-06-03 10:11:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644609793  

`using` ?

---

📁 include/boost/geometry/algorithms/append.hpp

```diff
 160 |+ template
 161 |+ <
 162 |+     typename Geometry, typename RangeOrPoint,
```

> Username: vissarion  
> Created_at: 2021-06-03 08:50:38 UTC  
> Updated_at: 2021-06-03 10:11:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644610462  

`typename RangeOrPoint` should go on the next line, right?

> Username: awulkiew  
> Created_at: 2021-06-03 10:24:07 UTC  
> Updated_at: 2021-06-03 13:23:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644675121  

This was done on purpose, there is enough free space so I kept the mandatory arguments in one line. Do you think this is wrong and I should put each one of them in separate lines?  
  
EDIT: I'll change it.

> Username: vissarion  
> Created_at: 2021-06-03 12:25:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644747500  

It is different from the guidelines but it makes sense so I am OK.


📁 include/boost/geometry/algorithms/detail/buffer/buffered_ring.hpp

```diff
  90 |+ struct concept_type<Geometry, geometry::detail::buffer::buffered_ring_collection_tag>
  91 | {
  92 |+     struct dummy {};
```

> Username: vissarion  
> Created_at: 2021-06-03 09:03:48 UTC  
> Updated_at: 2021-06-03 10:11:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644620263  

is this to disable concept checking for this case?

> Username: awulkiew  
> Created_at: 2021-06-03 10:25:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644675723  

Yes this is a dummy, empty concept check.


📁 include/boost/geometry/strategies/default_length_result.hpp

```diff
  23 |- #include <boost/variant/variant_fwd.hpp>
  24 |- 
  23 |+ #include <boost/geometry/algorithms/detail/select_geometry_type.hpp>
```

> Username: vissarion  
> Created_at: 2021-06-03 09:33:04 UTC  
> Updated_at: 2021-06-03 10:11:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644640976  

is this include needed here?

> Username: awulkiew  
> Created_at: 2021-06-03 10:27:16 UTC  
> Updated_at: 2021-06-03 10:27:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644676988  

Yes, it defines `detail::geometry_types<>` used at line 63.

> Username: vissarion  
> Created_at: 2021-06-03 12:23:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644746061  

right, select_geometry_type.hpp also includes the geometry_types ! thanks


📁 include/boost/geometry/algorithms/detail/select_geometry_type.hpp

```diff
  29 |+                               || util::is_geometry_collection<Geometry>::value
  30 |+ >
  31 |+ struct select_geometry_type
```

> Username: vissarion  
> Created_at: 2021-06-03 09:38:04 UTC  
> Updated_at: 2021-06-03 10:11:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644644412  

if this is only used by distance strategies why is it in algorithms ? Looks to me like a distance strategy utility, or I am missing something here?

> Username: awulkiew  
> Created_at: 2021-06-03 10:41:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644685633  

Technically they are used in files that are defined in `strategies/` directory, yes. These are `default_length_result`, `distance_result` and `comparable_distance_result`. They define the result of the algorithm defined by both geometries and strategy based on tag dispatching. This is why these files belong in the `algorithms/` directory, just like `area_result` which was already moved. I plan to move them there too as well, but not in this PR.  
  
`util::is_xxx` are multi-purpose traits that can be used anywhere.  
  
`detail::select_geometry_type` is a utility used only in algorithms and it allows to pick a type according to a predicate. And this type can either be the geometry itself or a type of a sequence defined by `traits::geometry_type<>` for GC or DG. So this is a single utility allowing us e.g. to choose a Geometry which has the most precise `coordinate_type` (used in `default_length_result`) or the most precise result of a strategy for a pair of geometries (`select_geometry_types` used in `distance_result`) for any Geometry the library supports.

> Username: vissarion  
> Created_at: 2021-06-03 12:21:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644745069  

OK, thanks for the explanations.


📁 include/boost/geometry/strategies/distance_result.hpp

```diff
  79 | 
  80 | 
  81 |- template
```

> Username: vissarion  
> Created_at: 2021-06-03 09:54:04 UTC  
> Updated_at: 2021-06-03 10:11:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644655418  

I cannot see why `AreGeometries` is not needed anymore is this related to the use of DynamicGeometries/GeomCollections ?

> Username: awulkiew  
> Created_at: 2021-06-03 10:46:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644688271  

It's because this was a part of the workaround for VS2015. With this compiler Boost.Variant disables variadic templates and emulates them instead. This means that at the end of the list of arguments there are types `boost::detail::variant::null` or something like that which are not geometries. `AreGeometries` was used to ignore them with VS2015 by setting the distance result to `int` for them. Now the adaptation for Boost.Variant handles it so this workaround including `AreGeometries` is not needed anymore.


📁 test/algorithms/clear_multi.cpp

```diff
  49 |-     test_geometry<mls>("MULTILINESTRING((0 0,0 1),(1 0,0 0))", 0);
  50 |-     test_geometry<mpoly>("MULTIPOLYGON(((0 0,0 1,1 0,0 0)),((10 0,10 1,11 0,10 0)))", 0);
  59 |+     test_geometry<Point>("POINT(0 0)", 1, 1);
```

> Username: vissarion  
> Created_at: 2021-06-03 10:03:33 UTC  
> Updated_at: 2021-06-03 10:11:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644661956  

why not `0` after clearing?

> Username: awulkiew  
> Created_at: 2021-06-03 10:19:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644672412  

Because Boost.Geometry Point can't be empty.


📁 test/algorithms/disjoint/test_disjoint.hpp

```diff
 110 |+ 
 111 |+     check_disjoint(id, wkt1, wkt2, v1, gc2, expected, no_strategy());
 112 |+     check_disjoint(id, wkt1, wkt2, gc1, v2, expected, no_strategy());
```

> Username: vissarion  
> Created_at: 2021-06-03 10:05:07 UTC  
> Updated_at: 2021-06-03 10:11:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#discussion_r644662896  

maybe add a test with gc and strategy?


---

## Comment 11

> Username: awulkiew  
> Created_at: 2021-06-03 14:04:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/850#issuecomment-853895543  

Thanks for the reviews!

---
