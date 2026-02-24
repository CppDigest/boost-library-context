# #53 - What is `circle_event.lower_x`? [Open]

> Username: lycantropos  
> Created at: 2020-11-30 14:33:55 UTC  
> Updated at: 2021-07-15 10:37:53 UTC  
> Url: https://github.com/boostorg/polygon/issues/53  

I'm trying to understand the algorithm (btw if there is a formal description -- will be glad to read it) and want to understand what is `circle_event.lower_x` field, I understand that `circle_event` class represents circle built during sweeping the plane and [from docstring](https://github.com/boostorg/polygon/blob/8a51f6f8fd2d87c852e95cfd202d38deb4000ce0/include/boost/polygon/detail/voronoi_structures.hpp#L231) I can see that  
> `lower_x_` - leftmost `x`-coordinate;  
  
For example, if we have a circle  
![image](https://user-images.githubusercontent.com/15193952/100622555-0b8b6c00-3332-11eb-8354-2c673e94faf8.png)  
does this point has coordinates `(lower_x, center_y)` or am I missing something?  
  
If `lower_x` is the lefmost should it be always to the left of the `center_x`?

---

## Comment 1

> Username: eadf  
> Created at: 2021-07-10 17:49:43 UTC  
> Updated at: 2021-07-15 10:37:53 UTC  
> Url: https://github.com/boostorg/polygon/issues/53#issuecomment-877676528  

`lower_x` is the `x` coordinate of the circle event *plus* the radius. It is used for sorting events in the correct order.  
Events are processed left to right, i.e low x value first -> higher x value has lower priority.   
So I guess the 'lower' part of the name refer to the priority, not the value.
