# #622 Fix/buffer pass strategies [Merged]

> Username: barendgehrels  
> Created at: 2019-10-02 14:37:40 UTC  
> Updated at: 2019-10-24 18:17:18 UTC  
> Merged at: 2019-10-24 18:17:14 UTC  
> Closed at: 2019-10-24 18:17:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/622  

This pull request takes care that strategies used within the buffer algorithm details are properly passed from one subroutine to another.  
  
Besides that, there are some changes in buffer test (which were already failing before this PR)  
  
And some additional changes related to removing the rescaling:  
* sectionalize  
* an extra condition in get_turn_info  
* a minor fix in the new subranges

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2019-10-09 09:08:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/622#pullrequestreview-299222737  

Thanks. I am ok.

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 372 |         , m_envelope_strategy(intersection_strategy.get_envelope_strategy())
 373 |         , m_expand_strategy(intersection_strategy.get_expand_strategy())
 374 |+         , m_point_in_geometry_strategy(intersection_strategy.template get_point_in_geometry_strategy<robust_point_type, robust_ring_type>())
```

> Username: vissarion  
> Created_at: 2019-10-09 07:43:31 UTC  
> Updated_at: 2019-10-23 19:53:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r332869468  

too long line, maybe

> Username: barendgehrels  
> Created_at: 2019-10-23 19:55:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r338247293  

Fixed

---

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 817 | 
 811 |-     static inline void determine_properties(piece& pc)
 818 |+     inline void determine_properties(piece& pc) const
```

> Username: vissarion  
> Created_at: 2019-10-09 09:01:10 UTC  
> Updated_at: 2019-10-23 19:53:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r332902113  

since you added `const` here you could also added to other non-static function members e.g. the other specialization of `determine_properties`

> Username: barendgehrels  
> Created_at: 2019-10-17 17:25:57 UTC  
> Updated_at: 2019-10-23 19:53:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r336131501  

Thanks, I missed your comments earlier.  
  
I can have a look there indeed, better if they are mutually consistent.  
And I will ook at the long line.

> Username: barendgehrels  
> Created_at: 2019-10-23 19:54:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r338247157  

Sorry, the other one cannot be made `const` because it modifies `m_pieces`.  
And this one should not be `static` anymore because it accesses member strategies.  
So current code is OK


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-16 19:26:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/622#pullrequestreview-302820303  

📁 include/boost/geometry/strategies/cartesian/point_in_poly_winding.hpp

```diff
  57 |-     typename CalculationType = void
  57 |+     typename CalculationType = void,
  58 |+     typename SideStrategyType = side::side_by_triangle<CalculationType>
```

> Username: awulkiew  
> Created_at: 2019-10-16 19:26:09 UTC  
> Updated_at: 2019-10-23 19:53:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r335665681  

The order of arguments is not correct I think. Is different `SideStrategyType` used anywhere? I cannot find it.

> Username: barendgehrels  
> Created_at: 2019-10-17 17:24:50 UTC  
> Updated_at: 2019-10-23 19:53:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r336130982  

I don't know why it is not correct - I mean, I can't add the strategy before calculation type without breaking existing functionality. Actually the whole structure is designed for backward compatibility, so maybe we should consider some more refactoring here.  
  
Anyway, I use it in another PR (still to come) so therefore you won't see it here. But it should be possible to use another side strategy here, instead of taking the default.

> Username: awulkiew  
> Created_at: 2019-10-19 15:56:35 UTC  
> Updated_at: 2019-10-23 19:53:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r336742755  

My point was that `CalculationType` typically is the last parameter. And yes this strategy could use some refactoring, i.e. Point types are not needed and the name is bad.  
  
Which other side strategy would you like to use? Maybe instead of adding `SideStrategyType` parameter to this strategy there should be another cartesian point-in-geometry strategy with its own side strategy different than this one?  
  
If it's not used in this PR should it be a part of it?

> Username: barendgehrels  
> Created_at: 2019-10-20 11:44:49 UTC  
> Updated_at: 2019-10-23 19:53:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r336775223  

Yes but my point was that inserting a parameter breaks existing code using it.  
I want to use another side strategy and/or the one Tinko created.  
What's the objection to having code more generic than it is now?

> Username: awulkiew  
> Created_at: 2019-10-21 13:22:12 UTC  
> Updated_at: 2019-10-23 19:53:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r337013628  

In general I think we're too careless with strategies and tend to choose the solution which locally works for a specific use-case but is not consistent with the rest of the library. Now we have many different strategies with various parameters in different orders, various naming schemes etc. Many of them are not documented and it is very hard for the users to know which strategy is expected by certain algorithm taking certain combination of arguments, etc.  
  
This particular strategy was agnostic in the past. It was moved as cartesian with the agnostic one as a wrapper left for backward compatibility. Additional strategies were added for other CSes. Now, a parameters is added for cartesian strategy only. Should the same parameter be added to spherical and geographic point-in-geometry strategies? Should it be used by the agnostic one too? What should the user expect? I understand that it may be easier to just pass a side strategy as template parameter but will it be also easier for the user vs. just passing different point-in-geometry strategy? Does the user even need that? Are strategies part of the public interface and therefore for the users? Or are these internal tools used mainly by us? Or maybe are there two kinds of strategies, some for the users and other for internal use?  
  
E.g. AFAIU you need this for the `buffer` internals, correct? Is this something the user should also be able to pass to an algorithm? E.g. assuming it's for the `buffer`, why not create a base class for internal use taking whatever parameters you like and then leave this strategy for the users as it was? If there is only one side strategy working for `buffer` in certain parts and another side strategy in other parts of the algorithm does it even have sense to allow to pass it by the user?  
  
So I guess I'd be ok with this change because it is not different than what can be found in other strategies right now. But in general I think this should either be avoided or we should think about the interface of the library at this point. So if there is some other solution that you'd also be happy with, like making this cartesian winding strategy an internal/detail strategy which then is used in the `buffer` and as a base class of the strategy for the users, I'd prefer that. Or is the solution you've choosen the best, given the strategies design we have right now?  
  
Side note: Initially I thought that being careful with strategies would be enough, so taking care about naming, adding more parameters etc. but I'm not sure now. I'm starting to lean towards the conclusion that the whole strategies system should be redesigned taking into account all of the complexity we have now and knowledge gathered over several last years. But obviously this is not a task for this PR.

> Username: vissarion  
> Created_at: 2019-10-21 14:10:05 UTC  
> Updated_at: 2019-10-23 19:53:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r337039294  

+1 for the side note

> Username: barendgehrels  
> Created_at: 2019-10-22 17:50:08 UTC  
> Updated_at: 2019-10-23 19:53:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r337661729  

@awulkiew : thanks for the note. You have done the most recent work on strategies so you know the current state the best. I was not 100% aware of the history about these winding strategies, it is quite long ago that I had worked on it.  
  
I agree with you that we should redesign the strategies. There are a few requirements (from my point of view):  
  
- all strategies should be flexible (the user, the the developer should be able to pass them)  
- mainly because of geographic/spherical: strategies might have a configuration (i.e. they should be passed at runtime)  
- strategies can depend on each other (such as here) (so we should first create a dependency graph)  
- there will be more  
  
So the change here fullfilled the flexibility, but (of course) not yet the rest indeed.  
  
For my current work on avoiding rescaling (not only buffer, in general) I'm using another side strategy. Therefore I should be able to use such another strategy, and it is really inconvenient that the cartesian winding strategy just takes hardcoded the triangle strategy.  
  
Even if I will not use such another strategy (not sure yet), strategies should support flexible strategies without hacking the code at those places.  
  
Good that you point out that it is now incompatible with the other winding or similar strategies. That was not my intention and I think that is not good. So I will revise that part (either taking the default strategy there, or something else, I will consider various options - keeping the fact that it will not take side_by_triangle there).  
  
It will be just a small change to this PR and I hope to finish it next Friday.

> Username: awulkiew  
> Created_at: 2019-10-22 21:25:25 UTC  
> Updated_at: 2019-10-23 19:53:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r337757972  

@barendgehrels To be clear. I'm not saying that the spherical and geographic strategy has to take side strategy as template parameter. Though it would certainly be an option. But AFAIK currently none of the algorithms needs that in non-cartesian CS. I was just expressing the problem we're facing here.  
  
But I don't understand something. Why do you plan to use different side strategy? Where in the code the decision about its use will be made? Will it be defined in some strategy? Will it always be used in buffer or will the user be able to choose it? Because if it's the user's decision, if it's important to use the correct side strategy for the task and buffer expects intersection strategy, shouldn't it be passed into the Intersection strategy too? It's the most top-level strategy passed into the buffer after all.  
  
On the other hand if it's defined specifically by you, only for buffer in some parts of the algorithm (with `side_by_triangle` used in other parts) because otherwise this algorithm does not work properly why does it have to be a part of the point-in-geometry strategy's interface if it's not the user who defines it?  
  
Do I understand correctly that two different side strategies are used in buffer in different places? If there will be only one in the end, why not simply change it in point-in-geometry and e.g. enable with `#define`?  
  
The problem with this issue is that if you think that the user has to be able to define side strategy in point-in-geometry strategy then by extension it has to be true for all higher-level strategies containing point-in-geometry strategy, so intersection strategy, point-segment distance strategy and IO strategy. And again by extension, in general case it should probably be possible to define all strategies you need explicitly. For that the whole system would have to be redesigned and the strategies for example all replaced with umbrella strategies.  
  
If it's used only in buffer I suggest to leave point-in-geometry strategies as they are now and implement additional one, with clear interface. E.g.:  
  
    template  
    <  
        typename SideStrategy, typename CalculateType = void  
    >  
    struct cartesian_point_in_geometry_strategy_for_buffer  
    {  
        BOOST_MPL_ASSERT((boost::is_same<typename SideStrategy::cs_tag, cartesian_tag>::value));  
    };  
  
Or am I missing something?  
  
I think I do not understand the use-case. Would it make sense to simply temporarily revert this change and propose it later together with another PR, with the code actually using it? So we can understand the problem better and talk about a specific problem then?

> Username: barendgehrels  
> Created_at: 2019-10-22 21:43:17 UTC  
> Updated_at: 2019-10-23 19:53:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r337764734  

To be clear from my side, I repeat  
  
> So I will revise that part (either taking the default strategy there, or something else, I will consider various options - keeping the fact that it will not take side_by_triangle there).  
  
So yes, I will revert that change. No problem.  
  
> it has to be true for all higher-level strategies containing point-in-geometry strategy, so intersection strategy, point-segment distance strategy...  
  
They don't belong there. Intersection strategy *only* depends on side-strategy and nothing more. Why should a complex algorithm as point-in-geometry be defined inside intersection-strategy, just calculating an intersection between two segments...  
  
But they are currently there indeed, most probably for pragmatic reasons (because that was the strategy originally passed). So I'm totally with you that that part should be redesigned.  
  
Why define another side-strategy? Because there might also appear another intersection strategy. But I come back to that later.

> Username: awulkiew  
> Created_at: 2019-10-22 22:49:19 UTC  
> Updated_at: 2019-10-23 19:53:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r337783665  

> So yes, I will revert that change. No problem.  
  
Thanks.  
  
> Intersection strategy only depends on side-strategy and nothing more. Why should a complex algorithm as point-in-geometry be defined inside intersection-strategy, just calculating an intersection between two segments...  
  
Sorry for confusion, by point-in-geometry strategy I mean the strategy used by point-in-geometry algorithm which is winding strategy. I started to call it point-in-geometry strategy because this strategy is used both for areal and linear geometries and not always used for calculation of winding number of point in polygon. And this is also how the getter is called in intersection strategy (`get_point_in_geometry_strategy()`).  
  
Or is your point that the intersection strategy should not define any getters at all? And that we should find a different solution for propagating strategies into the internal algorithms, like instead of passing "single-purpose" strategies (like intersection, winding, side, distance, etc.) to pass umbrella strategies gathering other "single-purpose" strategies together? If that's the case then I agree. I'm not sure about the umbrella part (maybe some other mechanism would be better) but for sure I'd prefer removing all of the getters and keep the functionality simple and well defined.  
  
But currently, with getters, if we wanted to not only use different side strategy but also some other like different winding strategy or area strategy then we'd have to pass it into the intersection strategy too (because this strategy is expected by the buffer and has to carry all CS-specific information right now). If we redesigned the strategies, removed the getters and replaced them by something else then indeed the intersection strategy would only depend on side strategy.

> Username: barendgehrels  
> Created_at: 2019-10-23 19:56:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r338247975  

Hi @awulkiew : So this part is reverted now.

> Username: barendgehrels  
> Created_at: 2019-10-23 20:00:13 UTC  
> Updated_at: 2019-10-23 20:00:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r338249564  

> Or is your point that the intersection strategy should not define any getters at all? And that we should find a different solution for propagating strategies into the internal algorithms, like instead of passing "single-purpose" strategies (like intersection, winding, side, distance, etc.) to pass umbrella strategies gathering other "single-purpose" strategies together? If that's the case then I agree.  
  
Yes! Intersection strategy should ideally not define any getters. It depends on side-strategy.  
Yes, we should propagate strategies as umbrella strategies, gathering them. But the umbrella is not trivial on itself, because it is hierarchical: side strategy (depends on nothing), intersection (depends on side), point-in-geometry (depends on side), distance (...).  
  
Anyway, that is a different thing now but it should keep our attention.

> Username: awulkiew  
> Created_at: 2019-10-24 12:18:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r338539360  

@barendgehrels Ok thanks!


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2019-10-24 12:18:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/622#pullrequestreview-306527346  

📁 include/boost/geometry/algorithms/detail/sections/sectionalize.hpp

```diff
 804 |+         // it is, the higher the risk to miss intersections. The larger it is,
 805 |+         // the more comparisons are made. So it should be on the high side.
 806 |+         detail::buffer::buffer_box(it->bounding_box, 0.001, it->bounding_box);
```

> Username: awulkiew  
> Created_at: 2019-10-24 12:18:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/622#discussion_r338539068  

This is probably also something which in the future should depend on a strategy somehow. As well as the epsilon used in point comparison (equals). But for now this harcoded value is fine.


---
