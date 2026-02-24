# #768 Support umbrella strategies in relops, setops, index, buffer and more. [Merged]

> Username: awulkiew  
> Created at: 2020-10-21 15:43:54 UTC  
> Updated at: 2021-02-16 12:32:18 UTC  
> Merged at: 2021-02-16 12:32:18 UTC  
> Closed at: 2021-02-16 12:32:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/768  

Also `is_simple`, `is_valid` and extensions like `dissolve` and `nsphere` relops.  
  
Add umbrella strategies:  
 - `relate`: used in setops, relops, `buffer`, *is_xxx*, `dissolve`, etc.  
 - `index`: used in `rtree`  
 - `io`: used in wkt read/write and extensions/shapefile  
  
Replace `point_box_by_side` agnostic strategy with CS-specific strategies.  
  
Fix passing of strategies (no defaults when user-defined strategy is  
passed) in places like buffer and distance.  
  
Alter tests accordingly.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2020-10-21 15:44:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#issuecomment-713670638  

Right now many algorithms use `relate` strategy. Should I create separate strategy for each algorithm derived from `relate` strategy for naming consistency? If yes, should I do this in this PR which is big already?

---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-21 15:54:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/768#pullrequestreview-513843132  

📁 include/boost/geometry/algorithms/buffer.hpp

```diff
 248 |-         >::type intersection_strategy;
 247 |+             GeometryIn, GeometryIn
 248 |+         >::type strategy;
```

> Username: awulkiew  
> Created_at: 2020-10-21 15:54:49 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r509406306  

So buffer uses the default relate umbrella strategy instead of intersection strategy.  
  
Right now buffer strategies are at the same time parameters/options for buffer e.g. holding distance value and CS-specific strategies defining how parts of buffer are calculated. I propose to separate that in the future.  
  
So this would be a separate buffer strategy passed by the user, also defining how specific buffer-related things are calculated (like generation of new points in various coordinate systems). And then I think `buffer()` algorithm should take parameters/options specifying distance/line-end/etc. plus one optional CS-specific umbrella strategy.

> Username: barendgehrels  
> Created_at: 2020-10-28 13:39:14 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r513449504  

This makes sense. I'm OK with separation.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-22 18:24:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/768#pullrequestreview-515003817  

📁 include/boost/geometry/strategies/relate/geographic.hpp

```diff
  42 |+     typename CalculationType = void
  43 |+ >
  44 |+ class geographic
```

> Username: awulkiew  
> Created_at: 2020-10-22 18:24:00 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r510368770  

Here is another question. Do you think that `SeriesOrder` is needed here? The only strategies that require it are area and segments intersection strategy. The order is used for different series in these two strategies. I'm considering removing this parameter from the umbrella strategies and sticking to the default based on `FormulaPolicy`. What do you think?

> Username: awulkiew  
> Created_at: 2021-02-16 12:24:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r576783446  

Do you have an opinion regarding it @vissarion @barendgehrels ?


---

## Review 4 [Approved]

> Username: barendgehrels  
> Created_at: 2021-02-03 11:44:44 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/768#pullrequestreview-518669044  

Sorry the review took so long.  
  
And I can't see that I reviewed every line - but I'm definitely in favour of getting the umbrella strategies!  
  
So of course approved.

---

## Comment 5

> Username: vissarion  
> Created_at: 2021-02-03 13:07:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#issuecomment-772493695  

Sorry for delays here, I will review in next few days.

---

## Review 6 [Approved]

> Username: vissarion  
> Created_at: 2021-02-12 10:33:30 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/768#pullrequestreview-589300613  

Thanks for this PR! It looks OK to me.

📁 include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp

```diff
 923 |     typename PointStrategy,
 924 |-     typename IntersectionStrategy,
 924 |+     typename Strategy,
```

> Username: vissarion  
> Created_at: 2021-02-12 09:39:27 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575096212  

why is this particular strategy renamed ?

> Username: awulkiew  
> Created_at: 2021-02-12 18:04:17 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575412935  

Because this is the umbrella strategy and the other ones are buffer-only strategies/parameters/selectors. I probably should've called it `Strategies` or `UmbrellaStrategy`.

> Username: vissarion  
> Created_at: 2021-02-15 10:21:43 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r576084920  

OK, I like `UmbrellaStrategy` naming but it's up to you.

---

📁 include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp

```diff
 964 |             end_strategy, point_strategy,
 965 |-             robust_policy, intersection_strategy.get_side_strategy());
 965 |+             robust_policy, strategy.side()); // pass strategy?
```

> Username: vissarion  
> Created_at: 2021-02-12 09:41:13 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575097230  

what does this comment mean here? pass the `strategy` instead of `strategy.side()`? and what is the difference of `strategy.side()` to `side_strategy` two lines above?

> Username: awulkiew  
> Created_at: 2021-02-12 18:06:30 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575414850  

`strategy` is the umbrella strategy `strategy.side()` returns side strategy from umbrella strategy (e.g. `side_by_strategy` or `side_by_cross_track`) and `side_strategy` is buffer's parameter/strategy/selector defining how a segment is enlarged by buffer on its side. Again, I should've probably called this variable `strategies`.


📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
1097 |-                     original.m_ring, m_point_in_geometry_strategy);
1038 |+                 = detail::within::point_in_geometry(point, original.m_ring,
1039 |+                                                     m_strategy);
```

> Username: vissarion  
> Created_at: 2021-02-12 09:52:49 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575104117  

should it be aligned with `=`

> Username: awulkiew  
> Created_at: 2021-02-12 18:18:08 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575425732  

I don't know, I tend to align function arguments with the rest of the arguments if I can.  
  
What exactly do you think should've been used here?  
```  
int const geometry_code  
    = detail::within::point_in_geometry(point, original.m_ring,  
        m_strategy);  
```  
```  
int const geometry_code  
    = detail::within::point_in_geometry(point,  
        original.m_ring, m_strategy);  
```  
```  
int const geometry_code  
    = detail::within::point_in_geometry(  
        point, original.m_ring, m_strategy);  
```  
The last one looks ok as well. So I can change it to the last one if you like.  
  
And in this case this is probably also possible:  
```  
using detail::within::point_in_geometry;  
int const geometry_code  
    = point_in_geometry(point, original.m_ring, m_strategy);  
```  
since the same number of lines is used.  
  
Or do you think about something else?

> Username: vissarion  
> Created_at: 2021-02-15 10:28:20 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r576089186  

Thanks for the details, I was also aligning function arguments with the rest of the arguments, but I am somehow convinced by https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers to wrap the lines (even this is not explicitly written there) so I am ok with any of your suggestions above.

> Username: mloskot  
> Created_at: 2021-02-15 10:31:20 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r576091113  

By the way, if it is possible that in future the library will achieve a compromise on using `clang-format` to ensure the consistent code format then, I think, it may be a good idea to pick the ones that `clang-format` can do best.  
  
In GIL, we are slowly working towards introducing consistent format for the whole codebase - around half of the code is still a mess - and to help new contributors we offer an example of `.clang-format` file, https://github.com/boostorg/gil/tree/develop/example/clang-format   
The configuration is not ideal as it does not cover 100% if preferred style, so it is a compromise, but it's better than mixture of many styles and new contributors can ensure they at least get close to what is expected.

> Username: awulkiew  
> Created_at: 2021-02-16 02:58:56 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r576520154  

I looked again and it seems that after putting all of the characters in one line here the line is shorter than some of the other lines in this file. Plus even in the guidelines we define max length as 100 characters. So I think this is the way to go here.  
  
Personally I don't have any preference regarding the formatting besides looking good and allowing to easily catch the sense of an expression. I guess that in various contexts the best formatting would look differently. In any case we should probably aim for the least amount of lines. In case of a function call we should keep the parameters in one block whatever it means. Closer to the max line length this could mean putting all of the parameters at the next line. If there were more space this could mean aligning the parameters to the first one, etc. The number of parameters will also matter because putting each of them at subsequent line will cause the lines bloat. The context will also matter because a function call somewhere in a block should probably be treated differently than in the `if` statement condition. So I'm in general sceptical about automatic formatting and I'd simply leave that to the programmer. But maybe clang-format is intelligent enough? IDK.

> Username: mloskot  
> Created_at: 2021-02-16 09:51:45 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r576689079  

> I don't have any preference regarding the formatting besides looking good  
  
Generally, the only preference I've got is consistency.  
  
> In any case we should probably aim for the least amount of lines.  
  
👍   
  
> I'm in general sceptical about automatic formatting and I'd simply leave that to the programmer.   
  
The `clang-format` is not ideal but it get close to almost any formatting style, depending on how sophisticated is team's preferences are. The compelling feature of `clang-format` is that it allows to achieve consistency with almost zero effort and it also helps me to review work by new contributors quicker as I don't have to explain over and over how to format, just point to `.clang-format` file and voila ;)   
  
Anyway, I meant a brief digression and not to open whole style debate, apologies.

> Username: awulkiew  
> Created_at: 2021-02-16 12:28:00 UTC  
> Updated_at: 2021-02-16 12:28:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r576785651  

Ok, I understand, no need to apologise.


📁 include/boost/geometry/algorithms/crosses.hpp

```diff
 117 |     {
  86 |-         typedef typename strategy::relate::services::default_strategy
 118 |+         //typedef typename strategies::crosses::services::default_strategy
```

> Username: vissarion  
> Created_at: 2021-02-12 09:55:36 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575105779  

is this comment needed?

> Username: awulkiew  
> Created_at: 2021-02-12 18:02:24 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575411318  

Probably not. Initally I though that maybe we'd like to have a separate umbrella strategy for each algorithm. But even if we wanted to implement then we'd probably do this as template aliases which means that the compiler would see all of them as the same umbrella relate strategy. And this means that they'd be interchangeable and relate strategy here could stay.


📁 include/boost/geometry/algorithms/detail/disjoint/interface.hpp

```diff
  72 |+                              Strategy const& strategy)
  73 |+     {
  74 |+         //using strategies::disjoint::services::strategy_converter;
```

> Username: vissarion  
> Created_at: 2021-02-12 09:57:28 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575106954  

is this needed?

> Username: awulkiew  
> Created_at: 2021-02-12 18:19:28 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575426983  

Probably not, I'll remove it.


📁 include/boost/geometry/algorithms/detail/disjoint/multipoint_geometry.hpp

```diff
 407 |+                 && ! detail::disjoint::disjoint_point_box(point, box_pair.first, m_strategy)
 408 |+                 && ! dispatch::disjoint
 409 |+                         <
```

> Username: vissarion  
> Created_at: 2021-02-12 10:05:54 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575112041  

4 spaces of indentation here, right?

> Username: awulkiew  
> Created_at: 2021-02-12 18:21:40 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575429149  

It'd look bad:  
```  
&& ! dispatch::disjoint  
    <  
        Point, single_type  
    >::apply(point, range::at(m_multi_geometry, box_pair.second), m_strategy))  
```  
  
In such cases I put as many tabs/spaces as it is needed to start the arguments list after the name of the function/struct starts.

> Username: vissarion  
> Created_at: 2021-02-15 10:29:11 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r576089757  

OK


📁 include/boost/geometry/algorithms/detail/distance/segment_to_box.hpp

```diff
  68 |+             // This is the only strategy defined in distance segment/box
  69 |+             // strategies that carries the information about the spheroid
  70 |+             // so use it for now.
```

> Username: vissarion  
> Created_at: 2021-02-12 10:12:33 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575115868  

can the umbrella strategy carry the spheroid information?

> Username: awulkiew  
> Created_at: 2021-02-12 18:28:14 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575435528  

It does hold the spheroid, this is why it is constructed from the side strategy here. An alternative would be to construct it from distance strategy but distance strategies were never passed into relops and setops so this backward compatibility mechanism does not support that. I'm now working on umbrella strategies for distance so this code will be replaced anyway.


📁 include/boost/geometry/algorithms/detail/equals/implementation.hpp

```diff
 144 | 
 145 | 
 146 |+ /*
```

> Username: vissarion  
> Created_at: 2021-02-12 10:14:54 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575117106  

so this is not needed? do we need it for future use?

> Username: awulkiew  
> Created_at: 2021-02-12 18:31:25 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575438426  

I don't know it seems not. I probably noticed that this is not used anywhere but was not sure. So in case I'm mistaken I simply comented it out instead of removing it.

> Username: vissarion  
> Created_at: 2021-02-15 10:30:18 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r576090495  

OK


📁 include/boost/geometry/algorithms/detail/equals/interface.hpp

```diff
 124 |+                              Strategy const& strategy)
 125 |+     {
 126 |+         //using strategies::equals::services::strategy_converter;
```

> Username: vissarion  
> Created_at: 2021-02-12 10:16:34 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575118105  

is this going to used in the future? why do you use `relate` instead now?

> Username: awulkiew  
> Created_at: 2021-02-12 18:30:41 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575437787  

Probably not. I'll probably leave relate here. I'm using relate everywhere to avoid creating an even bigger PR.


📁 include/boost/geometry/algorithms/detail/intersection/areal_areal.hpp

```diff
 114 |-                 pointlike::index, TupledOut
 115 |-             >::type pointlike_out_type;
 112 |+         //typedef typename geometry::tuples::element
```

> Username: vissarion  
> Created_at: 2021-02-12 10:17:27 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575118638  

should be removed?

> Username: awulkiew  
> Created_at: 2021-02-16 12:29:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r576786326  

done.


📁 include/boost/geometry/algorithms/detail/is_simple/areal.hpp

```diff
  44 |+     return ! boost::empty(ring)
  45 |+         && ! detail::is_valid::has_duplicates
  46 |                 <
```

> Username: vissarion  
> Created_at: 2021-02-12 10:19:23 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575119815  

one more space here ?

> Username: awulkiew  
> Created_at: 2021-02-12 18:39:54 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575446209  

The rationale is as follows: the number of spaces is the first multiple of 4 starting after the start of the struct's name. AFAIU you're saying that it should start exactly 4 spaces after the start of the struct's name. It requires less number of keys pressed to do it my way (1 or 2 tabs after enter vs tabs followed by spaces or backspaces) and it looks almost the same. But I'm flexible. ;)

> Username: vissarion  
> Created_at: 2021-02-15 10:32:30 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r576091796  

I am ok with this rationale

---

📁 include/boost/geometry/algorithms/detail/is_simple/areal.hpp

```diff
  54 |+ {
  55 |+     auto const end = boost::end(interior_rings);
  56 |+     return std::find_if(boost::begin(interior_rings), end,
```

> Username: vissarion  
> Created_at: 2021-02-12 10:20:25 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575120409  

indentation looks weird here

> Username: awulkiew  
> Created_at: 2021-02-12 18:42:15 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575448375  

How about this?  
```  
    return std::find_if(boost::begin(interior_rings), end,  
                        [&](auto const& r)  
                        {  
                            return ! is_simple_ring(r, strategy);  
                        }) == end; // non-simple ring not found  
```

> Username: vissarion  
> Created_at: 2021-02-15 10:32:59 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r576092054  

better, easier to read, thanks

> Username: awulkiew  
> Created_at: 2021-02-16 12:28:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r576786167  

Done.


📁 include/boost/geometry/algorithms/detail/overlay/get_turns.hpp

```diff
 193 |-                     disjoint_strategy_type()
 194 |-                 )
 190 |+         while(! detail::disjoint::disjoint_point_point(
```

> Username: vissarion  
> Created_at: 2021-02-12 10:26:36 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575123936  

a space is missing after `while`

> Username: awulkiew  
> Created_at: 2021-02-12 18:42:52 UTC  
> Updated_at: 2021-02-16 12:06:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#discussion_r575448759  

Indeed, a leftover from the previous version. I'll fix it.


---

## Comment 7

> Username: awulkiew  
> Created_at: 2021-02-16 12:32:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/768#issuecomment-779807567  

Thanks for the reviews!  
  
There are some things I'd like to add/modify but I'll do this is the next PR.

---
