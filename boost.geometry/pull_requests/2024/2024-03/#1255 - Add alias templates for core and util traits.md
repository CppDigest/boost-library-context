# #1255 Add alias templates for core and util traits [Merged]

> Username: awulkiew  
> Created at: 2024-03-03 22:24:28 UTC  
> Updated at: 2024-05-11 11:47:03 UTC  
> Merged at: 2024-05-11 11:47:03 UTC  
> Closed at: 2024-05-11 11:47:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1255  

Plus:  
Add C++17 variable templates for traits behind #ifdef  
Use aliases in several algorithms

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-03-05 19:29:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1255#pullrequestreview-1917942172  

📁 include/boost/geometry/algorithms/append.hpp

```diff
  63 |     {
  63 |-         typename geometry::point_type<Geometry>::type copy;
  64 |+         geometry::point_type_t<Geometry> copy;
```

> Username: barendgehrels  
> Created_at: 2024-03-05 19:29:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1255#discussion_r1513385737  

This first confused me.  
Because, what about `point_t`  ...  
  
But it is correct.  
  
From the standard:   
`using add_pointer_t = typename add_pointer<T>::type`  
`using common_type_t = typename common_type<T...>::type`  
  
We have to confirm to the second form.

> Username: awulkiew  
> Created_at: 2024-03-05 22:39:44 UTC  
> Updated_at: 2024-03-05 22:40:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1255#discussion_r1513584568  

Yes, the standard pattern unfortunately is what it is and for someone familiar with it `point_t` would probably be confusing.  
  
On similar note. I was thinking about adding aliases for Boost.Range traits like `typename boost::range_value<Range>::type` in `util/range.hpp`. C++20 adds this as `std::ranges::range_value_t<>`. In Boost.Geometry there is `namespace bg::range` already. This seems to be the best place for these aliases even though the word `range` is repeated. However I don't have a strong opinion about it, I see several possibilities:  
- `boost::geometry::range::range_value_t<>`  
- `boost::geometry::range_value_t<>`  
- `boost::geometry::range::value_t<>`  
- `boost::geometry::ranges::range_value_t<>` (probably the closest to the standard)  
  
Do you have any opinions?

> Username: barendgehrels  
> Created_at: 2024-03-06 08:23:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1255#discussion_r1514015149  

I would adhere to the standard. So that's the last one (incl. namespace) or the first one (with our namespace).

> Username: vissarion  
> Created_at: 2024-03-06 15:15:57 UTC  
> Updated_at: 2024-03-06 15:18:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1255#discussion_r1514665706  

I also agree with the 1st or last.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-03-05 19:33:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1255#pullrequestreview-1917948004  

📁 include/boost/geometry/algorithms/clear.hpp

```diff
  93 |     typename Geometry,
  93 |-     typename Tag = typename tag_cast<typename tag<Geometry>::type, multi_tag>::type
  94 |+     typename Tag = tag_cast_t<tag_t<Geometry>, multi_tag>
```

> Username: barendgehrels  
> Created_at: 2024-03-05 19:33:07 UTC  
> Updated_at: 2024-03-05 19:33:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1255#discussion_r1513389620  

I'll finish my review later (probably tomorrow) but this all looks very good, cool improvement. Way more readable.  
  
Thanks for all this work.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2024-03-05 21:48:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1255#pullrequestreview-1918197629  

📁 include/boost/geometry/util/type_traits_std.hpp

```diff
 214 | template <typename T>
 213 |- using bare_type = remove_cptrref<T>;
 215 |+ using bare_type [[deprecated]] = remove_cptrref<T>;
```

> Username: barendgehrels  
> Created_at: 2024-03-05 21:48:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1255#discussion_r1513540614  

I think this needs a comment saying why this is deprecated, and what is the replacement.

> Username: barendgehrels  
> Created_at: 2024-03-06 08:27:37 UTC  
> Updated_at: 2024-03-06 08:27:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1255#discussion_r1514020975  

Actually I was not aware of `cptrref` and find it hardly readable.  
`bare_type` is clearer.  
  
But anyway, there is `std::decay` too, is it equivalent? Can't we deprecate both and use that instead (if it works)?

> Username: awulkiew  
> Created_at: 2024-03-06 18:59:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1255#discussion_r1514998510  

I've used this name because in the standard there are:  
```  
remove_const<>  
remove_volatile<>  
remove_reference<>  
remove_pointer<>  
```  
and then there are:  
```  
remove_cv<>  
remove_cvref<>  
```  
The latter first removes reference and then const volatile. Because originally `bare_type` was first removing reference, then pointer, then cv (because there can be a reference to pointer) I named it:  
```  
remove_cptrref<>  
```  
If we wanted to remove volatile too which we probably should do I'd call it:  
```  
remove_cvptrref  
```  
but originally volatile was not removed so I didn't do it.  
  
AFAIU `std::decay` wouldn't remove pointer.


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2024-03-05 21:49:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1255#pullrequestreview-1918198065  

📁 include/boost/geometry/util/type_traits_std.hpp

```diff
 199 |- {
 200 |-     typedef std::conditional_t
 201 |+ struct [[deprecated]] add_const_if_c
```

> Username: barendgehrels  
> Created_at: 2024-03-05 21:49:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1255#discussion_r1513540886  

Also here, can you add a comment?


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2024-03-05 22:42:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1255#pullrequestreview-1918271588  

📁 include/boost/geometry/util/type_traits.hpp

```diff
 310 |- using util::is_areal;
 367 |+ template <typename T>
 368 |+ using is_areal [[deprecated]] = util::is_areal<T>;
```

> Username: awulkiew  
> Created_at: 2024-03-05 22:42:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1255#discussion_r1513587404  

Before you ask, I'll add an explanation here as well. :)


---

## Review 6 [Approved]

> Username: barendgehrels  
> Created_at: 2024-03-06 08:24:08 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1255#pullrequestreview-1919043661  

As said, all cool, thanks.  
We should add it to the docs too.

---

## Review 7 [Approved]

> Username: vissarion  
> Created_at: 2024-03-06 15:18:36 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1255#pullrequestreview-1920041382  

Thanks, looks nice.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2024-03-10 20:41:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1255#issuecomment-1987355826  

Thanks for the reviews. Should I merge it now or wait until the release to make bugfixing easiter?

---

## Comment 9

> Username: vissarion  
> Created_at: 2024-03-11 10:51:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1255#issuecomment-1988155098  

> Thanks for the reviews. Should I merge it now or wait until the release to make bugfixing easiter?  
  
I think it is simpler to merge it after the release.

---
