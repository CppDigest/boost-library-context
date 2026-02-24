# #409 - Fixing self-intersections in rings [Closed]

> Username: funchal  
> Created at: 2017-07-05 17:55:30 UTC  
> Updated at: 2024-10-02 10:43:39 UTC  
> Closed at: 2024-10-02 10:43:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/409  

I have a set of unoriented `ring`s, possibly (self-)intersecting, and I want to construct a `multi_polygon` from them, by just simply assuming that:  
- each outer ring is a polygon.  
- a ring inside of another is a hole.  
- a ring inside of a hole is another polygon, etc...  
  
I figured that, if I can convert each ring on its own to a polygon, then `sym_difference` should resolve intersections nicely, and give me what I want. However, sym_difference can't work with self-intersecting rings (assertion hit), so I am trying to figure out the easiest way to fix the self-intersections first.  
  
I think the best way to handle self-intersections in my case is to split the rings, so looking around I found `split_rings.hpp`. However:  
  
- [this line](https://github.com/boostorg/geometry/blob/e3b424940cdd524b640122ed4a05eba5dbe80fd0/include/boost/geometry/extensions/algorithms/detail/overlay/split_rings.hpp#L229) seems to have a bug since `detail::overlay::turn_operation` is used without its [template parameters](https://github.com/boostorg/geometry/blob/8012d17ca6f58615f243787d6849322ce4194f38/include/boost/geometry/algorithms/detail/overlay/turn_info.hpp#L48).  
- also, I understand that code is an extension, so I am asking whether there are any better/easier ways  to deal with this problem. I have considered using `self_turns` directly.  
  
Thanks!

---

## Comment 1

> Username: barendgehrels  
> Created at: 2017-07-06 16:23:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/409#issuecomment-313446881  

Hi,  
Did you also consider dissolve ? (which is also an extension, currently)  
Current released code does not provide this functionality...

---

## Comment 2

> Username: funchal  
> Created at: 2017-07-06 18:40:03 UTC  
> Updated at: 2017-07-06 18:40:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/409#issuecomment-313483360  

Hi there.  
  
Yes I did look at dissolve, but to be honest I don't really understand what exactly dissolve is doing (and my best recollection was that it also failed to compile unfortunately). Can I read about dissolve somewhere?  
  
I think fixing `split_rings` itself is not very difficult and I can try to submit a patch. I also think `split_rings` is a very intuitive way to resolve self-intersections in rings and perhaps it should be in the main library. In contrast, I think `dissolve` has a few heuristics in places and may be much harder to make a case of it being general purpose, which is why I was leaning towards using `split_rings`. Perhaps a part of this is fundamental and due to dissolve operating on polygons instead of rings.
