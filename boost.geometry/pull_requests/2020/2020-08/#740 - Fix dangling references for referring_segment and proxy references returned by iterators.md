# #740 Fix dangling references for referring_segment and proxy references returned by iterators. [Merged]

> Username: awulkiew  
> Created at: 2020-08-02 22:46:16 UTC  
> Updated at: 2020-08-10 18:27:38 UTC  
> Merged at: 2020-08-10 18:27:38 UTC  
> Closed at: 2020-08-10 18:27:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/740  

A fix for https://github.com/boostorg/geometry/issues/716  
  
It also removes some unneeded typedefs.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2020-08-05 12:34:19 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/740#pullrequestreview-461617849  

Thanks for the fix

📁 include/boost/geometry/algorithms/for_each.hpp

```diff
 178 |+                     is_const<Range>::value
 179 |+                 >::apply(previous, it, f);
 180 |             previous = it++;
```

> Username: barendgehrels  
> Created_at: 2020-08-05 12:33:06 UTC  
> Updated_at: 2020-08-10 10:43:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/740#discussion_r465692051  

This is quite a complex solution to add a const, or not...  
Isn't a simpler type possible?   
And in C++11/14 (with auto) maybe?  
  
Just questions, I'm fine anyway.

> Username: awulkiew  
> Created_at: 2020-08-05 17:01:27 UTC  
> Updated_at: 2020-08-10 10:43:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/740#discussion_r465872709  

I can't think of anything else besides encapsulating it inside `fe_range_per_segment_call_f` so at least it wouldn't be required to write it twice, here and below.  
  
At least as long as we want to preserve the current behavior. This is an implementation of `for_each_segment` and calling this for mutable ranges probably has no sense because there are no separate segments in them. If a user modifies a point of a mutable segment then another segment is modified indirectly. So a simple and reasonable fix would be to remove all of this `is_const` logic at all and always require the Range to be `const`. In this case also the `fe_range_per_segment_call_f` would be not needed at all because this implementation would handle both cases:  
```  
Point const& p0 = *it0;  
Point const& p1 = *it1;  
model::referring_segment<Point const> s(p0, p1);  
f(s);  
```  
  
Side info: `for_each_segment ` is not used anywhere in the library. At least for now. We use `segment_iterator` instead. So we could consider removing it in its entirety. But when we move to C++14 I think I'd prefer to use it (or rather implement a similar algorithm `for_each_segment_if` allowing to break the iteration) together with generic lambdas instead of `segment_iterator` because it's a lot lighter.  
  
@mloskot do you have some other idea?

> Username: barendgehrels  
> Created_at: 2020-08-05 20:43:58 UTC  
> Updated_at: 2020-08-10 10:43:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/740#discussion_r465992436  

Thanks for your explanation. Indeed - using `for_each_segment` (in `non const` mode) doesn't really make sense. But removing it is a breaking change.

> Username: mloskot  
> Created_at: 2020-08-05 20:58:51 UTC  
> Updated_at: 2020-08-10 10:43:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/740#discussion_r465999974  

Sorry, no better ideas. If nothing requires passing non-const, then narrowing the type qualification down to single possibility is certainly a good idea, and a  simplification.

> Username: awulkiew  
> Created_at: 2020-08-07 11:08:52 UTC  
> Updated_at: 2020-08-10 10:43:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/740#discussion_r466974224  

@barendgehrels ok, I simplified it preserving backward compatibility.

> Username: barendgehrels  
> Created_at: 2020-08-09 20:18:55 UTC  
> Updated_at: 2020-08-10 10:43:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/740#discussion_r467624490  

Cool! Thank you!


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2020-08-09 20:19:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/740#pullrequestreview-463900651  

📁 include/boost/geometry/algorithms/for_each.hpp

```diff
 105 |+             typename point_type<Range>::type
 106 |+         >::type,
 107 |+     bool UseRefernces =
```

> Username: barendgehrels  
> Created_at: 2020-08-09 20:19:17 UTC  
> Updated_at: 2020-08-10 10:43:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/740#discussion_r467624530  

Small typo, missing `e`

> Username: awulkiew  
> Created_at: 2020-08-10 10:43:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/740#discussion_r467821082  

Done.


---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2020-08-09 20:22:54 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/740#pullrequestreview-463900862  

📁 include/boost/geometry/algorithms/for_each.hpp

```diff
 140 |+         Point p0 = *it0;
 141 |+         Point p1 = *it1;
 142 |+         model::referring_segment<Point> s(p0, p1);
```

> Username: barendgehrels  
> Created_at: 2020-08-09 20:22:47 UTC  
> Updated_at: 2020-08-10 10:43:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/740#discussion_r467624867  

Clear, it's fine to me. I kind of see the problem. Is it ever used like this? But anyway, it supports what it did before. Thank you.  
  
(You can change `has` to `have`)

> Username: awulkiew  
> Created_at: 2020-08-10 10:32:59 UTC  
> Updated_at: 2020-08-10 10:43:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/740#discussion_r467816761  

I think using `for_each_segment` for mutable ranges doesn't make sense but since they were supported I decided to keep that behavior even for proxy references. We could talk about dropping it, in fact I have a next PR nearly ready related to for_each so we could talk about it there because this PR is fixing a bug.


---
