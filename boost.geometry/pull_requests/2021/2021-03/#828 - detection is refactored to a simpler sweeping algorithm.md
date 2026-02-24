# #828 [clusters] detection is refactored to a simpler sweeping algorithm [Merged]

> Username: barendgehrels  
> Created at: 2021-03-17 13:05:30 UTC  
> Updated at: 2021-04-07 09:13:27 UTC  
> Merged at: 2021-04-07 09:13:20 UTC  
> Closed at: 2021-04-07 09:13:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/828  

This fixes a part (around 30%) of the remaining errors in the recursive buffer check  
  
There are some (small) changes in expectations, because the previous clustering algorithm did not generate a cluster for some cases (for example in the distance_zero case) while it should have (they are at exactly the same locations).  
  
Apart from fixing several buffer cases, the new algorithm is also much simpler and more maintainable.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-03-17 13:07:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/828#pullrequestreview-614281314  

📁 include/boost/geometry/algorithms/detail/overlay/handle_colocations.hpp

```diff
  52 | namespace detail { namespace overlay
  53 | {
```

> Username: barendgehrels  
> Created_at: 2021-03-17 13:07:15 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r595996835  

Note that complex code below is all gone


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2021-03-17 13:08:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/828#pullrequestreview-614282314  

📁 include/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp

```diff
 291 |         add_segment_to(turn_index, op_index, point_to, op);
 292 |     }
```

> Username: barendgehrels  
> Created_at: 2021-03-17 13:08:15 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r595997633  

moved to separate headerfile (now kilo i/o giga)


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2021-03-19 13:14:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/828#pullrequestreview-616385263  

📁 include/boost/geometry/algorithms/detail/overlay/approximately_equals.hpp

```diff
  42 |+     constexpr calc_t maxlimit = 1.0e-3;
  43 |+     auto const limit = (std::min)(maxlimit, limit_kilo_epsilon * machine_kilo_epsilon * c);
  44 |+     return std::abs(a0 - b0) <= limit && std::abs(a1 - b1) <= limit;
```

> Username: awulkiew  
> Created_at: 2021-03-19 13:14:05 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r597667010  

The comment suggests that an alternative for this algorithm is the one using `distance`. `distance` would take all of the dimensions into account. This algorithm takes only 2 dimensions into account. Which one is correct? In general case should this be replaced with a N-dimensional version?  
  
----  
  
In `namespace math` there are:  
- `equals_by_policy` - https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/math.hpp#L196  
- `equals_with_epsilon` - https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/math.hpp#L575  
  
Maybe they could be used here instead of calculating all of this manually?  
If needed the policy in `equals_by_policy` could be replaced with lambda expression for convenience.

> Username: awulkiew  
> Created_at: 2021-03-19 13:19:40 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r597670841  

Furthermore, it would be possible to use distance here. You could simply pass Pt/Pt distance strategy or newly proposed umbrella strategy (https://github.com/boostorg/geometry/pull/812) and then use it directly here without including the algorithm.  
  
But have in mind that this code would become a lot more computionally intensive if distance was calculated. There would be additional `sqrt` call for cartesian and `sin/cos` calls for other coordinate systems.

> Username: barendgehrels  
> Created_at: 2021-03-24 11:15:32 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r600384115  

I moved this function. It's not new in this PR.  
But yes, I recently created it and it could be enhanced or replaced.  
The problem with the code from math is that it gives too high values for floating point (as indicated in the comment).  
I tried to replace it with comparable distance, and that works for cartesian but (again) requires an epsilon defined by the coordinate system, which will result in high values for float and therefore in similar code as is written here.  
The 3d variant we could make with your new `for_each_coordinate` functionality.  
  
For now I keep it here but we can discuss further about it.

> Username: barendgehrels  
> Created_at: 2021-03-24 11:51:20 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r600406948  

I did a test using `distance`. W.r.t. performance in Cartesian there is no measurable difference in this use case. Probably because it doesn't happen so often.  
But using geographical distance will cause problems:  
```  
false 1.6948370905798346708337476229644  
false 0.84474950387190805844994656581548  
false 0.41427736018131078310489101568237  
false 0.19008348922390885804567517425312  
false 0  
false 0  
```  
From the small test, the distance already returns zero if they are still far away (in terms of epsilon). Apart from that it's probably an overkill to use geographic distance there.  
  
So I propose to still keep it as it is here (and using `for_each` later)

> Username: awulkiew  
> Created_at: 2021-03-26 17:45:46 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r602483071  

`equals_with_epsilon` and `equals_with_policy` allows you to pass a value which is multiplied by machine epsilon before performing the test. Just like you did.

> Username: barendgehrels  
> Created_at: 2021-03-27 09:52:10 UTC  
> Updated_at: 2021-04-07 09:12:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r602698046  

I'll have a look again.

> Username: barendgehrels  
> Created_at: 2021-03-31 09:19:11 UTC  
> Updated_at: 2021-04-07 09:12:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r604733648  

:heavy_check_mark: thanks for the comments, done in new commit. I added a unit test too.  
_(I will squash commits before merging)_


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2021-03-19 13:34:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/828#pullrequestreview-616404489  

📁 include/boost/geometry/algorithms/detail/overlay/get_clusters.hpp

```diff
  36 |+     {
  37 |+         // Using machine epsilon results in too much points considered as inequal
  38 |+         return approximately_equals(p1, p2, 1.0); // 1000
```

> Username: awulkiew  
> Created_at: 2021-03-19 13:34:20 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r597681100  

I think it would be more intuitive if here a multiplier for machine epsilon was passed and there was no requirement on the caller's part to know that internally this number is multiplied by 1000 again before multiplying it by machine epsilon.  
  
Then the comment `// 1000` wouldn't be needed.

> Username: barendgehrels  
> Created_at: 2021-03-24 11:16:46 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r600384948  

The comment was left there by mistake

> Username: awulkiew  
> Created_at: 2021-03-26 17:48:17 UTC  
> Updated_at: 2021-04-07 09:12:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r602484661  

My point was that I propose to pass the value of epsilon multiplier here, so (1000), instead of a multiplier for another multiplier which is unknown unless you actually see the `approximately_equals` function definition.

> Username: barendgehrels  
> Created_at: 2021-03-31 09:21:27 UTC  
> Updated_at: 2021-04-07 09:12:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r604735257  

:heavy_check_mark: done


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2021-03-19 13:39:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/828#pullrequestreview-616409713  

📁 include/boost/geometry/algorithms/detail/overlay/get_clusters.hpp

```diff
  49 |+ #ifdef BOOST_GEOMETRY_USE_RESCALING_IN_GET_CLUSTERS
  50 |+ template <>
  51 |+ struct sweep_equal_policy<boost::long_long_type>
```

> Username: awulkiew  
> Created_at: 2021-03-19 13:39:41 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r597684918  

So this is a specialization for the type used for rescaling. What if a user passed a geometry using integral coordinates with rescaling disabled? There are bigger integral types than long long including e.g. MultiPrecision. My point is, shouldn't this be done for all integral coordinates?

> Username: barendgehrels  
> Created_at: 2021-03-24 11:57:19 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r600410872  

No, it's made for the rescaled integral grid.  
But indeed, other integral coordinates should return "exactly equal".  
:heavy_check_mark:


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2021-03-19 13:41:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/828#pullrequestreview-616411663  

📁 include/boost/geometry/algorithms/detail/overlay/get_clusters.hpp

```diff
 143 |+             {
 144 |+                 // Too far away in y-direction to be considered as equal
 145 |+                 break;
```

> Username: awulkiew  
> Created_at: 2021-03-19 13:41:35 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r597686400  

Why is y direction special?  
EDIT: It's probably just an arbitrary choice for sweeping. Or rather this is how the sweeping algorithm is typicaly presented. Which does not matter in cartesian, but it does in other coordinate systems.  
  
At this point I don't fully understand what clusters really are. But I'm going to ask the questions below anyway.  
  
AFAIU this is cartesian-only algorithm, is that correct?  
  
Have in mind that in spherical/geographic a segment vertex (the highest/lowest point of a segment) can be higher/lower (in terms of coordinates) than both of the endpoints. Note that this is no longer an issue when sweeping x-axis (longitudes) because then the longitudes of edge points define the range of coordinates. Then however there is a problem of antimeridian. On the other hand with y-sweeping (or maybe rather in both cases) there is a problem of polygons surrounding poles because they can go to the other side of the globe.  
  
So what with the other coordinate systems?  
And what do you think about rewriting this for x-coordinate if what I wrote above about spherical/geographic segments vertex affects this algorithm?

> Username: barendgehrels  
> Created_at: 2021-03-24 10:50:02 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r600367087  

Yes, it's an arbitrary choice.  
  
Clusters are locations with multiple turns. They are crucial to the traversal algorithm. It should work for either cartesian or non cartesian coordinates.  
  
I don't understand that x-coordinate would be better, in my opinion y-direction is better just because of those antimeridian points.  
It's all turn-based, so it's point-based. There are no segments or polygons involved (in the clusters). Of course turns are calculated from lines or polygons intersections, and they may span anything.  
  
Polygons surrounding poles or crossing the antimeridian are not problematic, unless there would be a cluster at (90) or (-90) (one point). I think that's a theoretical problem, it won't happen in practice (unless you make a testcase for it). The chance for a cluster on the -180 meridian is low too, but higher because it's a globe crossing line.

> Username: awulkiew  
> Created_at: 2021-03-25 02:53:25 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r601006035  

> Clusters are locations with multiple turns.  
  
So these are points that may be close to each other. These points are either endpoints or intersection points. And the purpose of this scanning is to find them and group close ones into clusters. Correct?  
  
If that's the case then I think indeed this particular algorithm should work in any CS. I guess the coordinates might be normalized but technically valid range is in (-180 -90, 180 90) so ok.  
  
----  
  
I'll explain what I was thinking about regarding vertices, segments, etc. Consider this:  
  
![path10](https://user-images.githubusercontent.com/1226951/112411043-87178b80-8d1c-11eb-81f4-150419cc08dd.png)  
  
In spherical/geographic CS, points of a segment can lie "above" its endpoints and any detected intersection point on this segment unless the intersection point is at the vertex or vertex of a geodesic is not between the endpoints. So in a hypothetical scenario of looking for segments/rings/etc. by scanning for endpoints or intersection points it is not possible to detect the segment correctly when the algorithm is scanning the latitudes.  
  
But since your goal (AFAIU) is only to group close points together, this is not a problem I think.

> Username: barendgehrels  
> Created_at: 2021-03-26 17:08:05 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r602458469  

> And the purpose of this scanning is to find them and group close ones into clusters. Correct?  
  
Correct  
  
> I'll explain what I was thinking about regarding vertices, segments, etc.  
  
Thanks! It's good to keep in mind if this algorithm (the sweeping part) is used more often. And yes, I then see what you mean and going in x-direction will be more convenient.

> Username: awulkiew  
> Created_at: 2021-03-26 17:31:42 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r602474335  

Ok, so the only problem is the normalization/winding because any point with latitude close to -90 or 90 should be considered equal to any other regardless of the longitude. And points with londitude -180 should be considered equal to points with longitude 180. The normalization functions we have would AFAIR set the longitudes to 0 for points at poles and longitude to 180 if points are -180. All of this is done WRT machine epsilon, this is done for points close to poles or antimeridian. So if your epsilon is greater than the machine epsilon then you'd have to normalize coordinates differently or take the CS into consideration by manually checking the closeness in corresponding way.  
  
EDIT: Btw, the simplest solution would be to dispatch closeness check here with `Strategies::cs_tag`.

> Username: awulkiew  
> Created_at: 2021-03-27 14:52:10 UTC  
> Updated_at: 2021-04-07 09:12:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r602728442  

I started wondering about one more thing. Since this is only for the checks if points are close to each other. Have you considered to do what we usually do when checking points of equality (which is actually closeness WRT machine epsilon), i.e. simply using `std::set` with a custom `less` making sure that close points are considered equal?  
  
EDIT: Though sorting one direction like you do will probably be faster.

> Username: barendgehrels  
> Created_at: 2021-03-28 17:52:09 UTC  
> Updated_at: 2021-04-07 09:12:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r602909495  

It's a good idea. I'll check it on Wednesday together with possible epsilon changes. Even with the sorting (which indeed should stay), I maybe can still use the set for the clusters.

> Username: barendgehrels  
> Created_at: 2021-03-31 11:15:06 UTC  
> Updated_at: 2021-04-07 09:12:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r604807339  

I have had a look. I'm not finished yet but I doubt if it is more convenient.   
  
Can you point me to the code where we actually really do this? Because with a custom less operator in two dimensions, points approximately considered equal, it's not that trivial. I've some testcode which (still) results in slightly different results and is more complex than what I had written originally.  
  
However, a unit test is added already and that is useful anyway. Plus code is split better. (see last commit)

> Username: awulkiew  
> Created_at: 2021-03-31 14:17:26 UTC  
> Updated_at: 2021-04-07 09:12:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r604938625  

Ok, not `std::set` but `std::binary_search` but it's the same story: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/disjoint/multipoint_geometry.hpp#L80-L83  
And I agree that this is technically incorrect because for 3 points: with coord1 = 0, coord2 = eps and coord3 = 2eps. The first one is equal to the second, the second to the third but the third is not equal to the first one. Which is inconsistent and I guess might cause assertion failure in some STL implementations.

> Username: barendgehrels  
> Created_at: 2021-03-31 17:14:55 UTC  
> Updated_at: 2021-04-07 09:12:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r605078373  

OK, thanks. Then I keep it as it was.  
  
Thanks for the review comments and the approval.


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2021-03-19 18:23:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/828#pullrequestreview-616685212  

📁 include/boost/geometry/algorithms/detail/overlay/get_clusters.hpp

```diff
 279 |+                 {
 280 |+                     auto v = it->second;
 281 |+                     for (auto i : v)
```

> Username: awulkiew  
> Created_at: 2021-03-19 18:23:20 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r597891130  

I understand using `auto` for turns, iterators and operations but here, is it really needed? Furthermore at line 280 the vector is copied. How about this?  
```  
for (std::size_t i : it->second)  
```

> Username: barendgehrels  
> Created_at: 2021-03-23 18:36:53 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r599836633  

Sure, much better. Thanks for your review! I'll address your comments tomorrow.

> Username: barendgehrels  
> Created_at: 2021-03-24 11:17:08 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r600385227  

:heavy_check_mark:


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2021-03-19 18:34:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/828#pullrequestreview-616693660  

📁 include/boost/geometry/algorithms/detail/overlay/get_clusters.hpp

```diff
 215 |+                         ? segment_distance(geometry0, first, second)
 216 |+                         : segment_distance(geometry1, first, second);
 217 |+         return sd == 1;
```

> Username: awulkiew  
> Created_at: 2021-03-19 18:34:28 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r597897423  

This code looks confusing to me. The function naming suggests that some kind distance is calculated. Since the geometry is passed and `segment` is in the name I jump to the conclusion that this is some kind of geometric distance. We can't deduce what's happening from the return type neither because `auto` is used. On the other hand the distance is compared with `1` which makes little sense.  
  
I think that explicitly stating the type `signed_size_type` would be a good start. I guess this name refers to the `std::distance()` but if we could change the name to something more descriptive I think this would be beneficial.

> Username: barendgehrels  
> Created_at: 2021-03-24 10:56:02 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r600371076  

It's the distance in terms of segments (including circular behavior). So two segments are consecutive if that distance is 1.  
I actually don't see much confusion here. Of course I can replace auto with `signed_size_type` indeed.

> Username: barendgehrels  
> Created_at: 2021-03-24 10:58:42 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r600372893  

:heavy_check_mark:


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2021-03-19 18:35:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/828#pullrequestreview-616694513  

📁 include/boost/geometry/algorithms/detail/overlay/get_clusters.hpp

```diff
 222 |+ 
 223 |+ template <typename Turn, typename Geometry0, typename Geometry1>
 224 |+ inline bool corresponding_turn(Turn const& turn, Turn const& start_turn,
```

> Username: awulkiew  
> Created_at: 2021-03-19 18:35:38 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r597898067  

Could this function be renamed? It's not clear in what way the turns correspond to each other.

> Username: barendgehrels  
> Created_at: 2021-03-24 10:53:12 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r600369145  

I think the name is OK but I understand your question.  
Note that the turns with `method_start` are additional. They were not necessary in rescaling, but they are added because sometimes (rare cases) the other turn (`method_touch` or `method_touch_interior`) is missing. Therefore I call it the corresponding turn.

> Username: barendgehrels  
> Created_at: 2021-03-24 11:00:55 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r600374421  

:heavy_check_mark: added description to the function


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2021-03-19 18:49:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/828#pullrequestreview-616704398  

📁 include/boost/geometry/algorithms/detail/overlay/get_clusters.hpp

```diff
 255 |+     std::map<segment_identifier, std::vector<std::size_t>> start_turns_per_segment;
 256 |+     std::size_t index = 0;
 257 |+     for (auto& turn : turns)
```

> Username: awulkiew  
> Created_at: 2021-03-19 18:49:28 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r597905748  

`auto const&` ?

> Username: barendgehrels  
> Created_at: 2021-03-24 10:51:06 UTC  
> Updated_at: 2021-04-07 09:12:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#discussion_r600367749  

:heavy_check_mark:


---

## Comment 11

> Username: awulkiew  
> Created_at: 2021-03-19 19:06:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#issuecomment-803049922  

Thanks! I labeled it as a bugfix but I don't know if that's correct.

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2021-03-24 11:41:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#issuecomment-805753537  

> Thanks! I labeled it as a bugfix but I don't know if that's correct.  
  
It's fine.

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2021-03-24 12:44:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#issuecomment-805789982  

@awulkiew : will commands marked as "fixup:" be squashed automatically on merge?

---

## Comment 14

> Username: awulkiew  
> Created_at: 2021-03-25 03:02:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#issuecomment-806328329  

@barendgehrels I don't know. If you want to squash the commits maybe simply use "Squash and merge" instead of "Create a merge commit"?  
  
Alternatively you could manually amend and force push:  
```  
git reset HEAD^  
git add .  
git commit --amend  
git push -f  
```

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2021-03-26 17:05:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#issuecomment-808376473  

>   
>   
> @barendgehrels I don't know. If you want to squash the commits maybe simply use "Squash and merge" instead of "Create a merge commit"?  
>   
> Alternatively you could manually amend and force push:  
>   
> ```  
> git reset HEAD^  
> git add .  
> git commit --amend  
> git push -f  
> ```  
  
OK, sure, I'll squash it before merging. I made a separate commit to show you what comments I processed.

---

## Comment 16

> Username: awulkiew  
> Created_at: 2021-03-26 17:41:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#issuecomment-808403153  

> I made a separate commit to show you what comments I processed.  
  
Ok, right.  
  
It seems my fix for compilation error in expectation_limits.hpp is conflicting with your fix. Sorry about that. I'm working on the release.

---

## Comment 17

> Username: barendgehrels  
> Created_at: 2021-03-27 09:50:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#issuecomment-808703855  

> It seems my fix for compilation error in expectation_limits.hpp is conflicting with your fix. Sorry about that. I'm working on the release.  
  
No problem. Thanks for your work on the release.

---

## Comment 18

> Username: barendgehrels  
> Created_at: 2021-03-31 17:16:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/828#issuecomment-811264376  

@vissarion I will squash, clean up a bit (there are some debug comments in the new unit test) and merge next Wednesday, probably, if you are OK with this PR.

---
