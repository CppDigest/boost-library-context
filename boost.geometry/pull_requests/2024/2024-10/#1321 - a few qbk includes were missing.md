# #1321 [fix] a few qbk includes were missing [Merged]

> Username: barendgehrels  
> Created at: 2024-10-02 13:18:45 UTC  
> Updated at: 2024-10-04 06:57:59 UTC  
> Merged at: 2024-10-04 06:57:54 UTC  
> Closed at: 2024-10-04 06:57:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1321  

* also fixed frechet/haussdorff documentation  
* removed ancient msvc support for doxygen_xml2qbk  
* fixes #1309

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-10-02 13:20:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1321#pullrequestreview-2342921390  

📁 doc/src/docutils/tools/doxygen_xml2qbk/doxygen_xml_parser.hpp

```diff
 406 |-         {
 407 |-             el.additional_description = node->value();
 408 |-             boost::trim(el.additional_description);
```

> Username: barendgehrels  
> Created_at: 2024-10-02 13:20:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1321#discussion_r1784519066  

This block is moved below. The actual bug was that some `.qbk` were missed because too explicit full paths.  
In the past the XML was apparently a bit different, because it did work before.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-10-02 13:21:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1321#pullrequestreview-2342925011  

📁 doc/src/docutils/tools/doxygen_xml2qbk/doxygen_xml_parser.hpp

```diff
 444 |         }
 445 |+         else if (boost::contains(full, ".qbk."))
 446 |+         {
```

> Username: barendgehrels  
> Created_at: 2024-10-02 13:21:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1321#discussion_r1784520626  

The code below replaces the block above but is a bit safer in terms of full paths.  
Also it should not handle sub nodes in some cases, to avoid duplicates.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2024-10-02 13:21:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1321#pullrequestreview-2342928207  

📁 include/boost/geometry/algorithms/discrete_frechet_distance.hpp

```diff
 229 |-        works for LineString-LineString) using specified strategy.
 228 |+ \brief \brief_calc2{discrete Frechet distance, between} \brief_strategy
 229 |+ \details \details_free_function{discrete_frechet_distance, discrete Frechet distance, between}.
```

> Username: barendgehrels  
> Created_at: 2024-10-02 13:21:52 UTC  
> Updated_at: 2024-10-02 13:21:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1321#discussion_r1784521914  

This was written in heading `Behavior` but for all other functions it is different. Harmonized that and the parameters below.  
Also the support is now as usual (not automatic though)


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2024-10-02 13:22:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1321#pullrequestreview-2342929359  

📁 include/boost/geometry/algorithms/discrete_frechet_distance.hpp

```diff
 244 | \* [link geometry.reference.strategies.strategy_distance_haversine Haversine (spherical)]
 245 |- [/ \* more (currently extensions): Vincenty\, Andoyer (geographic) ]
 245 |+ \* One of the geographic point to point strategies
```

> Username: barendgehrels  
> Created_at: 2024-10-02 13:22:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1321#discussion_r1784522400  

We still should document `Vincenty` and others

> Username: vissarion  
> Created_at: 2024-10-03 09:19:40 UTC  
> Updated_at: 2024-10-03 09:19:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1321#discussion_r1785903623  

Should we add a TODO here (although it is more general task than similarity distances)

> Username: barendgehrels  
> Created_at: 2024-10-03 19:37:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1321#discussion_r1786755708  

I'll make a separate issue for it.


---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2024-10-02 13:22:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1321#pullrequestreview-2342931517  

📁 include/boost/geometry/strategies/spherical/distance_haversine.hpp

```diff
 131 |-     to rounding error for short distances is:
 132 |-     d=2*asin(sqrt((sin((lat1-lat2) / 2))^2
 133 |-     + cos(lat1)*cos(lat2)*(sin((lon1-lon2) / 2))^2))
```

> Username: barendgehrels  
> Created_at: 2024-10-02 13:22:43 UTC  
> Updated_at: 2024-10-02 13:22:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1321#discussion_r1784523259  

This was not really necessary here and gave some layout errors.

> Username: vissarion  
> Created_at: 2024-10-03 09:17:16 UTC  
> Updated_at: 2024-10-03 09:19:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1321#discussion_r1785900509  

Could we keep it as a comment?

> Username: barendgehrels  
> Created_at: 2024-10-04 06:33:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1321#discussion_r1787225466  

✅ Moved to where more was written about the calculation


---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2024-10-02 13:23:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1321#pullrequestreview-2342935338  

📁 include/boost/geometry/strategies/spherical/distance_haversine.hpp

```diff
 127 | \qbk{
 128 | [heading See also]
 129 | [link geometry.reference.algorithms.distance.distance_3_with_strategy distance (with strategy)]
```

> Username: barendgehrels  
> Created_at: 2024-10-02 13:23:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1321#discussion_r1784524696  

So this was the actual bug, it was missing, but it is back now.  
  
![image](https://github.com/user-attachments/assets/a144a847-c883-4e61-a516-4cfd7543ea90)


---

## Review 7 [Commented]

> Username: barendgehrels  
> Created_at: 2024-10-02 13:25:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1321#pullrequestreview-2342941366  

📁 include/boost/geometry/algorithms/discrete_hausdorff_distance.hpp

```diff
 323 |+ \param geometry1 \param_geometry
 324 |+ \param geometry2 \param_geometry
 325 |+ \param strategy \param_strategy{point to point distance}
```

> Username: barendgehrels  
> Created_at: 2024-10-02 13:25:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1321#discussion_r1784527118  

Same as for Frechet. Now generates this doc:  
  
![image](https://github.com/user-attachments/assets/2db3061a-6c2e-4346-8e0e-182f44a5d244)


---

## Review 8 [Commented]

> Username: barendgehrels  
> Created_at: 2024-10-02 13:26:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1321#pullrequestreview-2342947341  

📁 doc/doxy/Doxyfile

```diff
 102 |                          details_calc2{2}="The free function \1 calculates the \2 of two geometries" \
 103 |                          details_calc2{1}="The free function \1 calculates the \1 of two geometries" \
 104 |+                          details_free_function{3}="The free function [^\1] calculates the \2 \3 two geometries" \
```

> Username: barendgehrels  
> Created_at: 2024-10-02 13:26:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1321#discussion_r1784529469  

The `\3` is for `between`. We can harmonize the macros later but I didn't want to make this PR too large.


---

## Review 9 [Approved]

> Username: vissarion  
> Created_at: 2024-10-03 09:19:57 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1321#pullrequestreview-2345075205  

Looks good to me, thanks!

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2024-10-04 06:35:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1321#issuecomment-2392929587  

Issue created: https://github.com/boostorg/geometry/issues/1323

---
