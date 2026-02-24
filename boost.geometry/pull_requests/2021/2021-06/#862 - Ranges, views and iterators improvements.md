# #862 Ranges, views and iterators improvements [Merged]

> Username: awulkiew  
> Created at: 2021-06-07 11:28:19 UTC  
> Updated at: 2021-06-30 21:11:27 UTC  
> Merged at: 2021-06-30 21:11:27 UTC  
> Closed at: 2021-06-30 21:11:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/862  

This PR makes changes allowing passing rvalue, temporary ranges and proxies without the need to explicitly create variables into `range::` utilities. Views and iterators are refactored to either allow to create them as temporaries or to simplify the code.  
  
The initial motivation was to replace this:  
```  
typename traits::interior_mutable_type<Polygon>::type  
    rings = geometry::interior_rings(poly);  
range::push_back(rings, std::move(ring));  
```  
or C++11 equivalent:  
```  
auto&& rings = geometry::interior_rings(poly);  
range::push_back(rings, std::move(ring));  
```  
with this:  
```  
range::push_back(geometry::interior_rings(poly), std::move(ring));  
```  
in case when `geometry::interior_rings(poly)` returns a temporary object like a proxy which is the case e.g. with Boost.Polygon adaptation.  
  
The list of the changes:  
- rvalue references are now allowed in `range::` utilities  
- pointers/references to ranges were removed from some iterators (`closing_iterator`) and views (`detail::closing_view`, `identity_view`) because the ranges pointed to could be destroyed temporaries and the pointers would be dangling  
- iterators conversion constructors were implemented using `enable_if` v.s. `static_assert`  
- `has_one_element` was moved to `detail::` namespace and moved to `detail/` directory  
- `base.hpp` file defining `detail::iterator_base` was moved to `detail/iterator_base.hpp` file  
- `detail::normalized_view` was renamed to `detail::reverse_close_view` and its implementation simplified  
- `detail::close_reverse_view` was added as a counterpart performing closing and reversing in the opposite order. In some of the functions we use one order and in the other functions the opposite. We should probably replace all places with the new `detail::close_reverse_view` because this one preserves the first point of the range.  
- `convert()` now preserves the first point of the range after the change from `reversible_view` followed by `closeable_view` to `detail::close_reverse_view`  
- `box_view` and `segment_view` were reimplemented (see the comments), `detail::point_view` removed and replaced with `std::array`.  
- `detail::range_type` was removed and its usage replaced with `ring_type`, the reason behind this is that it is not really needed and is not consistent semantically. It was the same as `ring_type` with the exception that it could define `box_view` which was semantically different. This is because `box_view` is not a view in the C++20 sense since it owns the points, it's a container or a geometry. Furthermore `detail::range_type` for Box was not used anywhere in the library.  
- `detail::as_range()` now can return proxy ranges by value as defined by the users in traits  
- added several aliases for views taking various arguments (see the "For the future" below):  
  - `detail::clockwise_close_view`  
  - `detail::clockwise_view`  
  - `detail::close_clockwise_view`  
  - `detail::close_view`  
  - `detail::reverse_view`  
- the `reverse` views takes Direction, the `clockwise` views takes Order, the `close` views takes Closure, the defaults for all arguments are taken from Range  
- added utility `detail::minimum_ring_size` taking Geometry instead of Closure  
- `closeable_view` and `reversible_view` usage replaced in algorithms, the utilities left for backward compatibility  
  
Currently we have three kinds of "views":  
- proper views in C++20 sense, constant time copy, not owning the base range, can be temporaries, etc. (e.g. `identity_view` or `detail::closing_view`)  
- geometries that doesn't own the base geometry (e.g. `detail::indexed_point_view`, `detail::two_dimensional_view`, probably `detail::boundary_view` but I have to look into it, it may be in yet another category corresponding to C++20 range factory). These are proxy geometries and probably should be named as such, e.g. `detail::point_of_indexed_proxy` or something similar.  
- geometries that owns the undelying geometries, which are copied from the source, (e.g. `box_view`, `segment_view`), these are proper geometries, of constant size like `array` or `static_vector` but just like them are still containers these are still geometries. So they should probably be moved entirely from `views/` and renamed to e.g. `static_ring` and `static_linestring` respectively.  
  
Do you have different ideas for the names?  
How to call a proxy geometry with one word? Consider "container" vs "range".  
  
I'm open to suggestions.  
  
----  
  
For the future:  
(actually not anymore since some of the issues mentioned below are addressed in this PR)  
  
Regarding the names, AFAIU the naming convention for C++20 range adaptors uses simple verbs or nouns in plural form (in one instance in past tense), e.g. `transform_view`, `reverse_view`, `elements_view`, `keys_view` (see: https://en.cppreference.com/w/cpp/ranges). So we could think about renaming our views and iterators according to this convention, or creating aliases to maintain backward compatibility or creating new ones. E.g. there would be `close_view` or `closed_view` but not `closing_view` or `closeable_view`. However C++20 `std::ranges::reverse_view` always reversed the range unlike the view defined in `reversible_view` so we could pick a different name for that, e.g. `conditionally_close_view` and `conditionally_reverse_view`. Note that the std metafunction allowing to pick a type at compile time is called `std::conditional`. Another alternative for `reverse_view` is a name with `clockwise` in it because this is what this view is used for in the library, to make rings clockwise. For that the parameter would have to be changed from Direction to PointOrder which would be semantically consistent with `close_view`, see below.  
  
Another thing is that `closeable_view` and `reversible_view` utilities are not needed. Distinguishing wheter or not close or reverse a range should be handled in the view itself.  
  
Yet another thing is that the arguments of `closeable_view` and `reversible_view` are not consistent semantically. `closeable_view` takes an atribute of a geometry, so whether or not a geometry is closed or opened and based on that doesn't close or does close respectively (so it does the opposite of the attribute passed as parameter). `reversible_view` on the other hand takes a parameter telling it directly what to do, so whether or not the range should be reversed or not.  
  
So I think these issues could be a base for future changes.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2021-06-11 17:33:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#issuecomment-859735157  

Thanks @awulkiew !  
I skipped this week, I will review this PR next Wednesday

---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2021-06-16 09:56:37 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/862#pullrequestreview-684947926  

Thanks for this, again, huge change and improvement. At many placesl it's becoming simpler, also because we can use C++14 now, and I like that.  
I'm not sure about all the namings, and there are still some TODO's left, but the PR in general is approved.

📁 include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp

```diff
 539 |         {
 540 |-             detail::normalized_view<RingInput const> view(simplified);
 540 |+             detail::reverse_close_view<RingInput const> view(simplified);
```

> Username: barendgehrels  
> Created_at: 2021-06-16 09:38:05 UTC  
> Updated_at: 2021-06-16 09:56:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r652524177  

Why is this a `reverse` view? This looks wrong to me. But maybe it's the name changes I don't fully understand yet.

> Username: barendgehrels  
> Created_at: 2021-06-16 09:44:22 UTC  
> Updated_at: 2021-06-16 09:56:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r652529004  

I believe I get it now. It is a view that **can** reverse and it **can** close, both if necessary.  
  
In the past this was the definition of **normalized**   
  
So it should be correct, but I actually prefer the old name (or another name)

> Username: awulkiew  
> Created_at: 2021-06-16 10:29:05 UTC  
> Updated_at: 2021-06-16 10:55:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r652561148  

This naming is taken from the C++20 convention. Have a look at this: https://en.cppreference.com/w/cpp/ranges  
e.g.: `drop_view`, `take_view`, `transform_view`, `split_view`, `join_view`, `reverse_view`. AFAIU the names are the same as corresponding algorithms/functions, i.e. do X with a range. Hence `reverse_close_view` which is short for `reverse_and_close_view`.  
  
Boost.Range have been using a different convention naming views by what a range becomes after applying a view, e.g. `reversed_view` is a range that was reversed. Using this convention it'd be `reversed_and_closed_view` or `reversed_closed_view`.  
  
I'm open to suggestion regarding the naming but I think that `normalized_view` is a wrong name because normalization may mean different things. So I'd prefer to name the views to mean precisely what they are doing or becoming, whatever this is.

> Username: awulkiew  
> Created_at: 2021-06-16 10:56:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r652578304  

I'd like to stress one more thing. The difference between these views and C++20 ones is that these are done conditionally unlike e.g. `std::ranges::reverse_view` which always reverses a range, just like `boost::reversed_view` or `std::reverse` or in fact `boost::geometry::reverse()`. This is why I added `conditionally` in the alternatives.

> Username: barendgehrels  
> Created_at: 2021-06-23 08:19:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r656868407  

Thanks for your explanations. It's clear and I agree. Indeed, normalization is ambiguous.  
`Conditionally` is OK, a bit long though.


📁 include/boost/geometry/views/detail/normalized_view.hpp

```diff
  99 |+ 
 100 |+ // Other possible names:
 101 |+ //   clockwise_closed_view
```

> Username: barendgehrels  
> Created_at: 2021-06-16 09:50:40 UTC  
> Updated_at: 2021-06-16 09:56:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r652533814  

Why do we need it actually? Because it looks identical to the `reverse_close_view`  
  
And indeed, a `clockwise_closed_view` is clockwise and closed, and that looks intuitive to me.

> Username: awulkiew  
> Created_at: 2021-06-16 10:38:22 UTC  
> Updated_at: 2021-06-16 11:12:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r652567120  

I described the reasoning at the bottom of the PR description. There are both views because in some cases `Direction` is used and in other cases `Order` is used. With `Order` semantically corresponding to `Closure`. In practice some algorithms pass `Reverse` template argument which is then translated to `Direction` while in other algorithms the `Order` is taken from Geometry and this is translated to `Direction`. So to avoid translations in many algorithms I added these redundant views doing the translation internally.  
  
Personally I think we need only one view. The one that first closes the geometry and then reverses it. There are two problems with this:  
- in some algorithms the different order of operations is performed so we have to check if the algorithm fails or not, e.g. `buffer()`  
- in many algorithms `Reverse` template argument is passed explicitly instead being taken from Geometry in place where it's needed  
After both of these are changed and tested we could have only one view. Furthermore, `Order`->`Direction` translation will not be needed anymore.

> Username: barendgehrels  
> Created_at: 2021-06-23 08:21:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r656870027  

I would welcome one view. We most probably might remove the `Reverse` template argument in those cases.  
So I used another order of operations in buffer, I was not aware of that. But it should result into the same source geometry, and therefore in the same buffer...

> Username: awulkiew  
> Created_at: 2021-06-23 10:23:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r656962524  

The different starting point is generated only for open, ccw rings and AFAIK we mainly test with closed cw rings.

---

📁 include/boost/geometry/views/detail/normalized_view.hpp

```diff
 122 |+     order_selector Order = geometry::point_order<Range>::value    
 123 |+ >
 124 |+ using close_clockwise_view = close_reverse_view
```

> Username: barendgehrels  
> Created_at: 2021-06-16 09:51:41 UTC  
> Updated_at: 2021-06-16 09:56:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r652534621  

Same here, why do we need it, and why do we need two variants in different orders? First reversing and then closing should have the same effect as closing and then reversing... or are there exceptions here?

> Username: awulkiew  
> Created_at: 2021-06-16 10:44:35 UTC  
> Updated_at: 2021-06-16 11:07:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r652571004  

I described this above, but to reiterate.  
  
> Same here, why do we need it, and why do we need two variants in different orders?  
  
Because in some algorithms `Reverse` parameter is passed and in other algorithms `Order` is taken from Geometry, so to avoid translating at least the `Order` to `Direction` in each algorithm this is done here once.  
  
> First reversing and then closing should have the same effect as closing and then reversing... or are there exceptions here?  
  
The difference is that when we first reverse and then close the first point may not be the same as in the original range if the range was opened. So the result may be different for closed and opened rings. Geometrically they are the same, but the results of some algorithms might be different. Currently we have algorithms performing these operations in two different orders and this PR is not intended to change that. It's because I didn't want to change the results of e.g. set operations or buffer for opened rings. This might also interfere with your rescaling changes. I think that we should change that in the future though but this requires testing.

> Username: awulkiew  
> Created_at: 2021-06-16 11:02:01 UTC  
> Updated_at: 2021-06-16 11:02:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r652582127  

One more thing, regarding:  
  
> It's because I didn't want to change the results of e.g. set operations or buffer for opened rings.  
  
The exception is `convert()` which I modified in this PR to preserve the first point for opened rings which makes the result strictly equal to the one for closed rings.

> Username: awulkiew  
> Created_at: 2021-06-16 11:44:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r652609222  

On second thought. Correct me if I'm wrong. AFAIR we mainly test algorithms for clockwise, closed geometries anyway. So I think it would be quite safe to change the order of closing and reversing even now. If anything this would make the results more consistent to what we test against. So I think I could change it now and remove the redundant views (`reverse_close_view` and `clockwise_close_view`) in this PR. What do you think?

> Username: barendgehrels  
> Created_at: 2021-06-30 08:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r661245550  

Was this question still relevant?  
I'm fine with it.

> Username: awulkiew  
> Created_at: 2021-06-30 09:24:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r661286267  

Not relevant, this was already done.


📁 include/boost/geometry/views/reversible_view.hpp

```diff
  41 |+ 
  42 |+ // Other possible names:
  43 |+ //   conditionally_reverse_view
```

> Username: barendgehrels  
> Created_at: 2021-06-16 09:52:19 UTC  
> Updated_at: 2021-06-16 09:56:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r652535096  

I would keep `reversible_view`  
Or maybe it's not the best name.  
So we want to view a range it as clockwise, regardless of the underlying orientation.  
`clockwise_view` as you have somewhere?

> Username: awulkiew  
> Created_at: 2021-06-16 10:49:57 UTC  
> Updated_at: 2021-06-16 11:10:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r652574388  

I'd prefer `clockwise_view` too but `reverse_view` takes `Direction` which either means "reverse" or "forward" and this has nothing to do with point order semantically. For this view to be replaced with `clockwise_view` we'd have to remove all instances of `Reverse` to `Direction` translation from the library and replace these with a view taking `Order` directly from Geometry.  
  
Well, I guess we could replace `Reverse` to `Direction` with `Reverse` to `Order` but this would be confusing.

---

📁 include/boost/geometry/views/reversible_view.hpp

```diff
  96 | 
  97 | template <typename Range>
  98 | struct reversible_view<Range, iterate_reverse>
```

> Username: barendgehrels  
> Created_at: 2021-06-16 09:54:27 UTC  
> Updated_at: 2021-06-16 09:56:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r652536731  

Ah, it's still here.

> Username: awulkiew  
> Created_at: 2021-06-16 10:50:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r652574713  

Yes, these metafunctions are here only for backward compatibility.


---

## Comment 3

> Username: awulkiew  
> Created_at: 2021-06-17 17:46:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#issuecomment-863437612  

@barendgehrels I decided to remove the redundant views, rename them and leave only the following:  
- `closed_view`  
- `clockwise_view`  
- `closed_clockwise_view`  
  
which I think better describes them (`closed` vs `close` and `reverse` not needed). My reasoning is that a verb could be used if a view always did something (like e.g. C++20 `reverse_view`), but here it's conditional and it's a representation of a different abstraction, i.e. closed range with clockwise order of points, a new thing, so here a noun is more suitable (like e.g. C++20 `elements_view`). Does it make sense?  
  
I think that `Reverse` to `Order` conversion looks good and is not confusing as I initially thought it'd be.  
  
So after this change the following are not used anymore:  
- `closeable_view`  
- `iterate_direction`  
- `order_as_direction`  
- `reversible_view`  
  
but left for backward compatibility

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2021-06-23 08:22:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#issuecomment-866635686  

> I decided to remove the redundant views, rename them and leave only the following  
  
This looks very clear to me! Agree!

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2021-06-23 08:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#issuecomment-866636576  

> but left for backward compatibility  
  
Can we clearly deprecate them? Then we can remove it earlier. I doubt if they are used from outside, but some people might make their own algorithms with our base functionality.

---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2021-06-23 08:27:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/862#pullrequestreview-690358118  

📁 include/boost/geometry/algorithms/convert.hpp

```diff
 170 |+                 geometry::closure<Range1>::value,
 171 |+                 Reverse ? counterclockwise : clockwise
 172 |+             >;
```

> Username: barendgehrels  
> Created_at: 2021-06-23 08:27:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r656874590  

Clear, I'm glad with this.  
One question, isn't it sufficient to just pass `Reverse` here? Do we need the ternary?  
I'm fine either way, you can decide.

> Username: barendgehrels  
> Created_at: 2021-06-23 08:33:22 UTC  
> Updated_at: 2021-06-23 08:33:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r656879207  

OK, I just reviewed the new definition of `closed_clockwise_view`. It makes sense like this, no need to change.  
And I now understand better that if we pass `Reverse`, it should be specified here; if not, it's retrieved automatically.  
  
But sometimes we need `Reverse` as a separate template parameter (for example for `difference` and therefore in general for all overlay), because we walk those geometries in opposite direction, on purpose.

> Username: awulkiew  
> Created_at: 2021-06-23 10:31:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r656967778  

Yes, this is why the Reverse to Order (and previously Reverse to Direction) translation is here.  
  
Another possibility would be to pass the Order instead of Reverse but I'd have to check all of the places to see if this is more clear.


---

## Comment 7

> Username: barendgehrels  
> Created_at: 2021-06-23 08:35:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#issuecomment-866644101  

I approved it already and approve it again :smiley:   
I hope it's merged soon, to avoid conflicts.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2021-06-23 10:38:45 UTC  
> Updated_at: 2021-06-29 09:21:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#issuecomment-866727749  

> Can we clearly deprecate them? Then we can remove it earlier. I doubt if they are used from outside, but some people might make their own algorithms with our base functionality.  
  
Yes, we could. In the docs and in the code with https://en.cppreference.com/w/cpp/language/attributes/deprecated  
But I think I'd prefer to do this in a different PR, and maybe at the beginning of the next version cycle.  
Is that reasonable?  
  
What do you think about renaming `box_view` and `segment_view` to e.g. `static_ring` and `static_linestring` (because they own the points)?  
What do you think about renaming e.g. `indexed_point_view` to e.g. `indexed_point_proxy` (because it contains a reference to geometry)?  
The reason would be to distinguish between views and other entities.

---

## Review 9 [Approved]

> Username: vissarion  
> Created_at: 2021-06-23 13:49:21 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/862#pullrequestreview-690657793  

Thanks I am OK with this PR.

📁 include/boost/geometry/algorithms/detail/convex_hull/interface.hpp

```diff
  93 |+         //       and back_insert_iterator<> can store a pointer to it.
  94 |+         // Handle linestring, ring and polygon the same:
  95 |+         auto&& range = detail::as_range(out);
```

> Username: vissarion  
> Created_at: 2021-06-23 13:29:57 UTC  
> Updated_at: 2021-06-23 13:49:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r657103028  

I am not sure if `auto` is of good use here since it is difficult to derive the type of `range`

> Username: awulkiew  
> Created_at: 2021-06-24 16:13:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r658091902  

What do you mean by "it is difficult to derive the type of `range`"?  
  
Do you mean that you'd prefer the following? Or do you have something else in mind?  
```  
typename ring_return_type<Geometry>::type range = detail::as_range(out);  
```  
  
Btw, I'm working on a different PR in which this will be removed.

> Username: vissarion  
> Created_at: 2021-06-29 08:13:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r660384838  

> What do you mean by "it is difficult to derive the type of `range`"?  
>   
> Do you mean that you'd prefer the following? Or do you have something else in mind?  
>   
> ```  
> typename ring_return_type<Geometry>::type range = detail::as_range(out);  
> ```  
  
yes this is what I mean, it is more clear   
  
> Btw, I'm working on a different PR in which this will be removed.  
  
OK so there should not be any issue then.

> Username: awulkiew  
> Created_at: 2021-06-29 09:52:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r660460770  

> OK so there should not be any issue then.  
  
I'm using `auto&&` there too, e.g.:  
```  
auto&& ring = exterior_ring(polygon);  
```  
so it's possible that we'll have the same discussion there.  
  
I'll try to explain how I see this and maybe you'll agree. If not please explain why do you think this this is harder to understand because I think otherwise.  
  
In general `typename ring_return_type<Geometry>::type` is generic and can be of arbitrary type. In Boost.Geometry in majority of cases this is a reference but it can be a proxy type since AFAIR this is defined as a trait. However to a person not knowing how Boost.Geometry works on the first sight this looks like a value (not reference). At least I think this is what a random person would think. That this line would be a copy:  
```  
typename ring_return_type<Geometry>::type range = detail::as_range(out);  
```  
  
In the case of universal reference `auto&&` it's clear that range is of generic type and can be a reference or value. So I think that it's more clear than the alternative.


📁 include/boost/geometry/algorithms/detail/equals/collect_vectors.hpp

```diff
 339 |-     template <typename NormalizedRange>
 340 |-     static inline void apply_impl(Collection& collection, NormalizedRange const& range)
 334 |+     template <typename RevClRange>
```

> Username: vissarion  
> Created_at: 2021-06-23 13:38:27 UTC  
> Updated_at: 2021-06-23 13:49:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r657110575  

What `RevClRange` means? Reverse closed or reversed clockwise range? `normalized_view` was renamed to  `closed_clockwise_view` should NormalizedRange be renamed accordingly, or I misunderstood something?

> Username: awulkiew  
> Created_at: 2021-06-24 16:17:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r658095239  

Yes, I'll change the name.


📁 include/boost/geometry/algorithms/detail/is_valid/has_duplicates.hpp

```diff
  60 |+         auto const end = boost::end(view);
  61 |+         auto next = it;
  62 |+         for (++next; next != end; ++it, ++next)
```

> Username: vissarion  
> Created_at: 2021-06-23 13:42:45 UTC  
> Updated_at: 2021-06-23 13:49:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r657114637  

why not   
```  
        auto it = boost::begin(view);  
        auto next = it;  
        for (++next; next != boost::end(view); ++it, ++next)  
```  
?

> Username: awulkiew  
> Created_at: 2021-06-24 16:19:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r658096306  

Because AFAIU otherwise the new end iterator has to be created in each iteration.

> Username: vissarion  
> Created_at: 2021-06-29 08:20:25 UTC  
> Updated_at: 2021-06-29 08:20:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#discussion_r660389797  

OK, if there is a creation in `boost::end(view)` I agree.


---

## Comment 10

> Username: vissarion  
> Created_at: 2021-06-29 08:22:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#issuecomment-870386824  

> > Can we clearly deprecate them? Then we can remove it earlier. I doubt if they are used from outside, but some people might make their own algorithms with our base functionality.  
>   
> Yes, we could. In the docs and in the code with https://en.cppreference.com/w/cpp/language/attributes/deprecated  
> But I think I'd prefer to do this in a different PR, and maybe at the beginning of the next version cycle.  
> Is that reasonable?  
>   
> What do you think about renaming `box_view` and `segment_view` to e.g. `static_ring` and `static_segment` (because they own the points)?  
> What do you think about renaming e.g. `indexed_point_view` to e.g. `indexed_point_proxy` (because it contains a reference to geometry)?  
> The reason would be to distinguish between views and other entities.  
  
I am OK will all, except `static_ring`, why not `static_box`?

---

## Comment 11

> Username: awulkiew  
> Created_at: 2021-06-29 09:21:24 UTC  
> Updated_at: 2021-06-29 09:24:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#issuecomment-870429868  

> I am OK will all, except `static_ring`, why not `static_box`?  
  
@vissarion Because this geometry stores 4 or 5 points and is adapted to Ring concept.  
  
Furthermore I made a mistake in the name `static_segment`. It should've been `static_linestring` (I edited my question). The reason is the same. They both store copies of points, have size known at compile-time and are adapted to Ring and Linestring concept. In other words these are specific types of Ring and Linestring.

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2021-06-30 08:28:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#issuecomment-871202399  

> > > Can we clearly deprecate them? Then we can remove it earlier. I doubt if they are used from outside, but some people might make their own algorithms with our base functionality.  
> >   
> >   
> > Yes, we could. In the docs and in the code with https://en.cppreference.com/w/cpp/language/attributes/deprecated  
> > But I think I'd prefer to do this in a different PR, and maybe at the beginning of the next version cycle.  
> > Is that reasonable?  
> > What do you think about renaming `box_view` and `segment_view` to e.g. `static_ring` and `static_segment` (because they own the points)?  
> > What do you think about renaming e.g. `indexed_point_view` to e.g. `indexed_point_proxy` (because it contains a reference to geometry)?  
> > The reason would be to distinguish between views and other entities.  
>   
> I am OK will all, except `static_ring`, why not `static_box`?  
  
I'm OK with these new names. And indeed I would prefer to have it in a different PR.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2021-06-30 21:10:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/862#issuecomment-871730075  

Thanks for the reviews! I'll make other changes we talked about in a different PR.

---
