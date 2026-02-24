# #582 Feature/add start turn [Merged]

> Username: barendgehrels  
> Created at: 2019-04-03 12:03:50 UTC  
> Updated at: 2019-07-18 11:56:58 UTC  
> Merged at: 2019-05-01 20:08:02 UTC  
> Closed at: 2019-05-01 20:08:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/582  

This PR is another preparation of rescaling:  
  
- adding start turns, which are sometimes necessary in case other turns are missed  
- some more decisions based on very small nearly-collineary but not completely situations  
- fixing empty output in traversal

---

## Review 1 [Commented]

> Username: vissarion  
> Created_at: 2019-04-11 09:53:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/582#pullrequestreview-225434311  

📁 include/boost/geometry/algorithms/detail/overlay/get_distance_measure.hpp

```diff
  49 |+     // (for example include or exclude turns), which are checked later
  50 |+     // with other conditions.
  51 |+     bool is_small() const { return std::fabs(measure) < 1.0e-3; }
```

> Username: vissarion  
> Created_at: 2019-04-11 09:49:07 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r274344229  

any clue about this number? does it come from experimentations?

> Username: awulkiew  
> Created_at: 2019-04-11 14:50:28 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r274463630  

Also, probably: `math::abs(measure)`.

> Username: barendgehrels  
> Created_at: 2019-04-15 18:16:20 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r275484508  

The source is in the comments, right above - it is a more or less arbitrary number.  
It is a FP number so fabs should be fine - but I could make it abs too

> Username: vissarion  
> Created_at: 2019-04-16 08:42:52 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r275690366  

Does this mean that changing this number affects the output/correctness or the performance of the algorithm?

---

📁 include/boost/geometry/algorithms/detail/overlay/get_distance_measure.hpp

```diff
  93 |+         typedef CalculationType ct;
  94 |+ 
  95 |+         // Construct a line in general form (ax + by + c = 0),
```

> Username: vissarion  
> Created_at: 2019-04-11 09:50:10 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r274344629  

if this is only for cartesian shouldn't it be in strategies?

> Username: barendgehrels  
> Created_at: 2019-04-15 18:17:21 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r275484875  

I did not make it a real strategy, invokeable by users - it is a detail, so for now I kept it here. Unless you object.

> Username: vissarion  
> Created_at: 2019-04-16 08:40:11 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r275689186  

Ok, but maybe add a comment or TODO that this should be moved to strategies in the future?

> Username: awulkiew  
> Created_at: 2019-04-16 13:15:22 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r275789463  

> I did not make it a real strategy, invokeable by users  
  
It's not a matter of invoking by the user since it should still be derived from higher-level strategy like intersection strategy in this case.  
  
I guess we could keep it here (at least for now) but could you at least take the coordinate system from strategy instead of geometry? You could add `typedef ... cs_tag` member type to intersection strategies for this purpose.  
  
Actually we could do this with all strategies in order to generate CS-specific parts of the algorithm if needed instead of defining strategies within strategies.

> Username: awulkiew  
> Created_at: 2019-04-16 13:18:54 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r275791156  

But there is another thing I don't understand. AFAIU this is a utility to make sides calculation more robust, correct? consistent with distance calculation? If that's the case why is this not a part of side strategy? Or does it have a different purpose?

> Username: barendgehrels  
> Created_at: 2019-04-17 10:26:33 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r276175515  

Working at this:  
  
> I guess we could keep it here (at least for now) but could you at least take the coordinate system from strategy instead of geometry? You could add typedef ... cs_tag member type to intersection strategies for this purpose.  
  
But I'm a puzzled - what value does it add? I assume you find it more configurable because that tag comes from intersection strategy, and not from geometry, and they can be passed independently.  
  
We now use the basic intersection strategy as an umbrella strategy to access all other strategies. So yes, I agree that a cs_tag could be added there. But I think it should be splitted again - one basic intersection strategy (as we had before) and a separate umbrella strategy returning you all the other strategies (area, point-in-geometry, envelope, etc) which all have nothing to do with intersection strategy itself (that, as far as I can see, needs only a side strategy as a dependency).  
  
Anyway, that is a different subject. To make it just a bit more consistent with current implementations I will add that tag - but I'm really hesitant to add an accessor there again for this new strategy.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2019-04-16 17:31:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/582#pullrequestreview-226804636  

- Yes, I can write a TODO  
- Yes, OK, I probably can get the CS from strategy and pass it explicitly  
- Integrating it into side-strategy, that was my first version indeed - but the Cartesian side-by-triangle uses the area to get it, this uses the distance which should be more precise. It is about very small distances. Besides that, area works well to give the rough side, but here we're not interested in a threshold based on area, because it is unrelated to area. It is the distance that counts... Therefore I separated it.  
- So yes, indeed, it should make it consistent with distance calculation  
- Indeed, changing the value (a bit) should not matter. I will come back to this.

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 181 |+             // Not truely collinear, distinguish for union/intersection
 182 |+             // If p goes left (positive), take that for a union
 183 |+             ui_else_iu(dm.is_positive(), ti);
```

> Username: barendgehrels  
> Created_at: 2019-04-15 18:18:35 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r275485310  

I have to change this, to account for IndexP / IndexQ

> Username: barendgehrels  
> Created_at: 2019-04-17 10:28:41 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r276176247  

>     * Indeed, changing the value (a bit) should not matter. I will come back to this.  
The value itself is indeed not sensitive, it can be changed a bit without changing behaviour. Of course if you change it completely, things solved by this feature might start to fail.


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2019-04-17 13:22:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#issuecomment-484087396  

@awulkiew : I applied your remark, defining the cs_tag in the intersect_segment strategies and pass them in get_turn_info to distance_measure. Because it is called from basehandler it has to be passed several times. I now named the strategy **ubrella_strategy** which makes it in my eyes much clearer.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2019-05-01 07:42:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#issuecomment-488228607  

OK to merge this?

---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2019-05-01 16:17:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/582#pullrequestreview-232639126  

📁 include/boost/geometry/algorithms/detail/overlay/get_distance_measure.hpp

```diff
 136 |+ 
 137 |+ template <typename CalculationType>
 138 |+ struct get_distance_measure<CalculationType, spherical_equatorial_tag>
```

> Username: awulkiew  
> Created_at: 2019-05-01 16:17:38 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r280123810  

This is not really needed since spherical intersection/umbrella strategy defines `spherical_tag`.

> Username: barendgehrels  
> Created_at: 2019-05-01 19:58:53 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r280194891  

OK


---

## Comment 6

> Username: awulkiew  
> Created_at: 2019-05-01 16:24:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#issuecomment-488331934  

Yes, sorry for late response. I'm ok with merging.  
  
> I now named the strategy ubrella_strategy which makes it in my eyes much clearer.  
  
Sure, it's at the same time umbrella strategy and intersection strategy. In the future we could think about implementing proper umbrella strategies. E.g. in this case all set and relational operations could take one `relate` umbrella strategy, etc.

---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2019-05-01 18:17:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/582#pullrequestreview-232688305  

📁 include/boost/geometry/algorithms/detail/overlay/get_distance_measure.hpp

```diff
 150 |+ };
 151 |+ template <typename CalculationType>
 152 |+ struct get_distance_measure<CalculationType, geographic_tag>
```

> Username: awulkiew  
> Created_at: 2019-05-01 18:17:37 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r280162310  

This specialization is the same as the one for `spherical_tag` so it could be implemented as:  
  
    template <typename CalculationType>  
    struct get_distance_measure<CalculationType, geographic_tag>  
        : get_distance_measure<CalculationType, spherical_tag>  
    {};

> Username: barendgehrels  
> Created_at: 2019-05-01 19:58:43 UTC  
> Updated_at: 2019-05-01 20:06:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#discussion_r280194821  

OK - will do this and merge, thank you


---

## Comment 8

> Username: barendgehrels  
> Created_at: 2019-05-01 20:34:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/582#issuecomment-488411604  

> Sure, it's at the same time umbrella strategy and intersection strategy. In the future we could think about implementing proper umbrella strategies. E.g. in this case all set and relational operations could take one `relate` umbrella strategy, etc.  
  
Right, OK

---
