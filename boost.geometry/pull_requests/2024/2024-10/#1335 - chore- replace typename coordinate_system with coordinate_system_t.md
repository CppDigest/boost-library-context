# #1335 chore: replace typename coordinate_system with coordinate_system_t [Merged]

> Username: barendgehrels  
> Created at: 2024-10-31 21:00:36 UTC  
> Updated at: 2024-12-14 11:13:02 UTC  
> Merged at: 2024-11-01 18:35:05 UTC  
> Closed at: 2024-11-01 18:35:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1335  

and add detail meta function coordinate_system_unit  
  
Next PR in series. This one a bit shorter. A few more to come.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2024-11-01 09:50:43 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1335#pullrequestreview-2409798568  

LGTM, thanks!

📁 include/boost/geometry/core/coordinate_system.hpp

```diff
 104 |+ 
 105 |+ // Short cut for coordinate system units
 106 |+ template <typename Geometry>
```

> Username: vissarion  
> Created_at: 2024-11-01 09:49:20 UTC  
> Updated_at: 2024-11-01 09:50:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1335#discussion_r1825645338  

I probably missing something here but why not simply   
```  
template <typename Geometry>  
using coordinate_system_units_t = typename coordinate_system_units<Geometry>::type::units;  
```

> Username: barendgehrels  
> Created_at: 2024-11-01 18:34:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1335#discussion_r1826167864  

That was my first version. But `XXX_t` is in general always an alias for the meta function `typename XXX::type` and I did not want to deviate from that convention. So let's keep it like that.


---
