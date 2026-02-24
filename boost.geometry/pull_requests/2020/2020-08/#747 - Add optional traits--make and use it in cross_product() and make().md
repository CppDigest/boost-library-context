# #747 Add optional traits::make and use it in cross_product() and make(). [Merged]

> Username: awulkiew  
> Created at: 2020-08-26 18:37:15 UTC  
> Updated at: 2020-11-25 20:31:18 UTC  
> Merged at: 2020-11-25 20:31:17 UTC  
> Closed at: 2020-11-25 20:31:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/747  

This change addresses the backward compatibility issue raised here: https://github.com/boostorg/geometry/pull/745  
  
With the new optional trait it is possible to define how a geometry like Point, Box or Segment is constructed.  
  
`bg::make()` for 2d and 3d points is `constexpr` now if the trait is specialized.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2020-08-27 08:27:30 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/747#pullrequestreview-476492316  

Looks OK to me. Thanks!  
As an addition, it could be useful to add some basic unit tests.

📁 include/boost/geometry/geometries/point_xyz.hpp

```diff
 132 |+     static const bool is_specialized = true;
 133 |+ 
 134 |+     static constexpr point_type apply(CoordinateType const& v0,
```

> Username: vissarion  
> Created_at: 2020-08-27 08:21:14 UTC  
> Updated_at: 2020-11-25 14:24:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/747#discussion_r478242445  

do you need 3 coordinates here?

> Username: awulkiew  
> Created_at: 2020-08-27 11:44:19 UTC  
> Updated_at: 2020-11-25 14:24:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/747#discussion_r478354382  

Yes, I'll fix it.

---

📁 include/boost/geometry/geometries/point_xyz.hpp

```diff
 133 |+ 
 134 |+     static constexpr point_type apply(CoordinateType const& v0,
 135 |+         CoordinateType const& v1)
```

> Username: vissarion  
> Created_at: 2020-08-27 08:24:14 UTC  
> Updated_at: 2020-11-25 14:24:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/747#discussion_r478244256  

indentation issue

> Username: awulkiew  
> Created_at: 2020-08-27 13:29:08 UTC  
> Updated_at: 2020-11-25 14:24:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/747#discussion_r478419381  

done


---

## Comment 2

> Username: awulkiew  
> Created_at: 2020-08-27 11:44:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/747#issuecomment-681897206  

> it could be useful to add some basic unit tests.  
  
Right.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2020-08-27 11:50:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/747#issuecomment-681899495  

What do you think about moving `point_xy` and `point_xyz` from `model::d2` and `model::d3` namespace into `model` namespace? Of course leaving the alias for backward compatibility for now. These additional namespaces seems to be not needed. Or is there a reason why they are there?

---

## Comment 4

> Username: vissarion  
> Created_at: 2020-08-27 13:27:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/747#issuecomment-681948653  

> What do you think about moving `point_xy` and `point_xyz` from `model::d2` and `model::d3` namespace into `model` namespace? Of course leaving the alias for backward compatibility for now. These additional namespaces seems to be not needed.   
  
I am ok with that.  
  
>Or is there a reason why they are there?  
  
@barendgehrels should know

---

## Comment 5

> Username: awulkiew  
> Created_at: 2020-11-24 23:08:26 UTC  
> Updated_at: 2020-11-25 14:24:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/747#issuecomment-733284758  

@barendgehrels @mloskot are you ok with this change?

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2020-11-25 18:26:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/747#issuecomment-733877811  

>   
>   
> > What do you think about moving `point_xy` and `point_xyz` from `model::d2` and `model::d3` namespace into `model` namespace? Of course leaving the alias for backward compatibility for now. These additional namespaces seems to be not needed.  
>   
> I am ok with that.  
>   
> > Or is there a reason why they are there?  
>   
> @barendgehrels should know  
  
I'm OK with the move. But it is not backwards compatible, users might use it. We should add it into the release notes then. And not for 1.75 then...

---

## Review 7 [Approved]

> Username: barendgehrels  
> Created_at: 2020-11-25 18:27:30 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/747#pullrequestreview-538757173  

I'm OK, sorry for the delay.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2020-11-25 20:29:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/747#issuecomment-733932139  

@barendgehrels Thanks!  
  
> I'm OK with the move. But it is not backwards compatible, users might use it. We should add it into the release notes then. And not for 1.75 then...  
  
Yes, it's too late for that. I think we should add aliases in `d2` and `d3` namespaces and this way this change would be backward compatible.

---
