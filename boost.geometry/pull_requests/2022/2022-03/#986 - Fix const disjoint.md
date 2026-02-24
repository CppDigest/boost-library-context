# #986 Fix const disjoint [Merged]

> Username: barendgehrels  
> Created at: 2022-03-16 13:23:08 UTC  
> Updated at: 2022-04-13 12:52:49 UTC  
> Merged at: 2022-04-13 08:09:53 UTC  
> Closed at: 2022-04-13 08:09:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/986  

Fixes #985   
  
Some pieces of the touched code become a bit simpler by this (for example avoiding an extra conversion).  
  
There are some renamings as well, such as `get_turns` (this was really inconvenient, there is a namespace and a free function as well - and this does something different - so I call it `turn_retriever` now) and `define_turn_operation_type` i/o `turn_operation_type` (because that is there as well).  
  
Also there are some white space changes (at end of lines), fixed by my editor.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2022-03-16 13:25:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/986#pullrequestreview-911624852  

📁 include/boost/geometry/algorithms/detail/sections/section_functions.hpp

```diff
 154 |-                              Box const& other_box,
 155 |-                              RobustPolicy const& robust_policy)
 152 |+ inline bool exceeding(int dir,
```

> Username: barendgehrels  
> Created_at: 2022-03-16 13:25:21 UTC  
> Updated_at: 2022-03-16 13:25:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r828005971  

These two are unrelated but should not be declared as `static`


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2022-03-16 13:26:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/986#pullrequestreview-911627249  

📁 test/algorithms/relate/relate_const.cpp

```diff
  37 |+     BOOST_CHECK_EQUAL(exp_touches, bg::touches(geometry1, geometry2));
  38 |+     BOOST_CHECK_EQUAL(exp_relate, bg::relate(geometry1, geometry2, bg::de9im::mask("F0F******")));
  39 |+     BOOST_CHECK_EQUAL(exp_within, bg::within(geometry1, geometry2));
```

> Username: barendgehrels  
> Created_at: 2022-03-16 13:26:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r828007622  

So it fixes not only disjoint, but many boolean relation operators


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2022-03-30 08:53:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#issuecomment-1082805226  

Hi @awulkiew and @vissarion , can you please review this PR

---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2022-04-08 11:26:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/986#pullrequestreview-936305558  

📁 include/boost/geometry/algorithms/detail/overlay/get_turns.hpp

```diff
 931 |           typename TagBase1 = typename topological_tag_base<Geometry1>::type, typename TagBase2 = typename topological_tag_base<Geometry2>::type>
 932 |- struct turn_operation_type
 932 |+ struct define_turn_operation_type
```

> Username: awulkiew  
> Created_at: 2022-04-08 11:26:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846015592  

What is the reasoning behind this change? Verbs are typically used for functions or metafunctions semantically modifying a type. Consider that `point_type` is not named `define_point_type`.

> Username: barendgehrels  
> Created_at: 2022-04-08 14:06:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846148430  

It was confusing - there is another `turn_operation_type` as well, with another function.  
Yes, I know, `define_` is here extra but a `point_type` is not duplicate.  
  
If you use duplicate names and search for them through the code - all occuring many times - and want to refactor something, it's inconvenient that there are multiple with different functions.

> Username: awulkiew  
> Created_at: 2022-04-08 23:59:38 UTC  
> Updated_at: 2022-04-08 23:59:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846544714  

What different `turn_operation_type` are you talking about? I tried to find it but I only see typedefs. AFAIU this is the only `struct` of this name. So if my understanding is correct this is the same case as it is with `geometry::point_type<>::type` and `typedef ... point_type`. Or am I missing something?

> Username: barendgehrels  
> Created_at: 2022-04-09 06:35:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846583712  

Yes, it’s probably the only struct indeed. All right, I will rename it back, it’s easier than it was to make it unique.


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2022-04-08 11:39:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/986#pullrequestreview-936318256  

📁 include/boost/geometry/algorithms/detail/relate/turns.hpp

```diff
  54 | >
  54 |- struct get_turns
  55 |+ struct turn_retriever
```

> Username: awulkiew  
> Created_at: 2022-04-08 11:39:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846024854  

Why was this name changed? What was the original problem? That it's confused with the original `get_turns`?  
  
Anyway this is semantically not a class which object is created somewhere like a functor/predicate whatever (it's not a retriever created and used somewhere) but implementation of an algorithm with static member function `apply()`. We use verbs for these in the library right?  
  
BTW, if I was implementing this now it'd be free function `detail::relate::get_turns()`.

> Username: barendgehrels  
> Created_at: 2022-04-08 14:07:21 UTC  
> Updated_at: 2022-04-08 14:07:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846149111  

See the PR's description: `get_turns` is a free function which does something else.

> Username: awulkiew  
> Created_at: 2022-04-09 00:09:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846546496  

Yes, `geometry::get_turns()` is a free function, `dispatch::get_turns` is a struct and `detail::get_turns_generic` is a struct as well (the implementation). `detail::relate::turns::get_turns` is an implementation as well. The only difference between `geometry::get_turns()` and `relate::turns::get_turns::apply()` is that parameters common across all relops and L/L setops are set here internally so the caller is not forced to pass them each time the same way. As I mentioned it could as well be implemented as a free function. It's neither a retriever nor a getter it's an implementation of an algorithm `get_turns()` specific for relops.

> Username: barendgehrels  
> Created_at: 2022-04-09 06:36:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846583741  

I will also rename this back.


---

## Comment 6

> Username: awulkiew  
> Created_at: 2022-04-08 11:52:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#issuecomment-1092781039  

It looks good to me in general but you mixed fixing the "const" issue with renaming of interfaces. This is out of scope of this PR potentially interferes with my work on the support of GCs in relops (I haven't checked how much). Not to mention I think the names are incorrect as I mentioned in the comments.  
  
I also don't understand the confusion. `turns::get_turns::apply()` calls `get_turns` algorithm with some of the parameters defined. Would it be more intuitive for you if it was a free function `relate::get_turns()`?

---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2022-04-08 12:01:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/986#pullrequestreview-936341241  

📁 include/boost/geometry/algorithms/detail/relate/boundary_checker.hpp

```diff
 114 |-             for ( multi_iterator it = boost::begin(m_geometry) ;
 115 |-                   it != boost::end(m_geometry) ; ++ it )
 118 |+             for (auto const& ls : m_geometry)
```

> Username: awulkiew  
> Created_at: 2022-04-08 12:01:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846041393  

This is incorrect. MultiLinestring has to be Boost.Range not STD range.  
  
In general we cannot use range based for loop for a geometry that can be passed by the user, only for geometries and containers that we create ourselves internally.

> Username: barendgehrels  
> Created_at: 2022-04-08 14:10:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846152158  

All right I will revert this. Thanks.

> Username: barendgehrels  
> Created_at: 2022-04-08 14:25:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846166999  

:heavy_check_mark:


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2022-04-08 12:11:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/986#pullrequestreview-936351458  

📁 include/boost/geometry/algorithms/detail/relate/linear_areal.hpp

```diff
1303 |+                 Pi, Pj, Pk,
1304 |+                 Qi, Qj, Qk
1305 |+             > side_calc(pi, pj, pk, qi, qj, qk, strategy);
```

> Username: awulkiew  
> Created_at: 2022-04-08 12:11:44 UTC  
> Updated_at: 2022-04-08 12:11:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846048435  

If that's the preferred way then I suggest to remove `la_side_calculator` entirely and replace function calls like `pk_wrt_p()` with corresponding strategy calls.

> Username: barendgehrels  
> Created_at: 2022-04-08 14:11:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846153528  

I don't want to make the PR bigger. I don't say it's the preferred way - it is a more convenient way than before, because it does not need conversions.

> Username: awulkiew  
> Created_at: 2022-04-09 00:00:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846544801  

Ok got it.


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2022-04-08 12:17:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/986#pullrequestreview-936357391  

📁 include/boost/geometry/algorithms/detail/relate/linear_areal.hpp

```diff
1272 |-                 return calculate_from_inside_sides(side_calc);
1261 |+                 // Calculate sides with different points for P and Q
1262 |+                 return calculate_from_inside_sides(qi, turn.point, pi, qi, turn.point, qj, strategy);
```

> Username: awulkiew  
> Created_at: 2022-04-08 12:17:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846052626  

AFAIR the conversion was done because sometimes point type of one geometry is used to create a segment with the point type of another geometry. But AFAIS side strategies are able to take three different points so I think it should work but is it tested?

> Username: barendgehrels  
> Created_at: 2022-04-08 14:12:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#discussion_r846154428  

Yes, side strategies should take three different point types.


---

## Comment 10

> Username: awulkiew  
> Created_at: 2022-04-08 13:02:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#issuecomment-1092836013  

As I mentioned in one of the previous PRs related to const points this issue is confusing to me. I'd like to do some testing myself because I didn't get an answer then. I suspect that the cause of this issue is somewhere else. The changes made in this PR should be irrelevant.  
  
Furthermore this is not about const points but about points defining only `get()`. You test point can be assigned to, it defines copy ctor and assignment operator, etc. It's not about copying. It is possible to copy it or store in a container. You couldn't use it in a linestring if it was otherwise.  
  
There is also a separate issue which is that your test point doesn't define default ctor. This also has nothing to do with constness. It's a different issue.

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2022-04-08 14:09:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#issuecomment-1092902788  

> It looks good to me in general but you mixed fixing the "const" issue with renaming of interfaces. This is out of scope of this PR potentially interferes with my work on the support of GCs in relops (I haven't checked how much). Not to mention I think the names are incorrect as I mentioned in the comments.  
>   
  
Yes, I could have split it - but then you would see a name change and right after a PR with a contents change. I thought there was no problem in one go for lines touched anyway.  
  
I don't think it will interfere (a lot), this is not specific for any geometry type. Apart from that: with splitted PR's it interfers even more.

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2022-04-08 14:17:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#issuecomment-1092910747  

>   
  
I answered your point in the previous PR. See the file `const_point.hpp` defining such a point type, and indeed: with no setters and no default constructor. It's exactly this type that I encountered in practice. And you could implement first one (with default constructor) and then this one (without default constructor) but what's the point... It touches nearly the same code.  
  
Maybe the name is not perfect, I mean a point type which no setters and no default constructor (effectively it can't be changed after construction, therefore a `const_point` is not that bad. It is a `non_mutable_point`

---

## Comment 13

> Username: awulkiew  
> Created_at: 2022-04-09 00:17:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#issuecomment-1093512201  

I get your point now. So there are several issues addressed by your recent PRs which may be confusing:  
1. support for point types defining only getters  
2. support for point types without default constructor  
3. refactoring  
  
1 and 2 are often used together in the library because of the semantics of some utilities used in the library, e.g. `point_on_border` or `envelope` which requires to create an object with default ctor and then to set the coordinates. So it indeed makes sense to address these issues together.  
  
I'd only refrain from doing refatoring at the same time because this would limit the scope of the PR. E.g. now instead of simply talking about the important issues 1 and 2 we have to also talk about naming etc.

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2022-04-09 06:40:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#issuecomment-1093747268  

> I get your point now. So there are several issues addressed by your recent PRs which may be confusing:  
>   
> 1. support for point types defining only getters  
> 2. support for point types without default constructor  
> 3. refactoring  
>   
> 1 and 2 are often used together in the library because of the semantics of some utilities used in the library, e.g. `point_on_border` or `envelope` which requires to create an object with default ctor and then to set the coordinates. So it indeed makes sense to address these issues together.  
>   
> I'd only refrain from doing refatoring at the same time because this would limit the scope of the PR. E.g. now instead of simply talking about the important issues 1 and 2 we have to also talk about naming etc.  
  
Thank you. So we go for 1 and 2. About 3: I understand and will rename it back. And I will not create a follow-up, because apparently the new names are not so easy to choose, so it’s fine then. It was still useful for me in the process of adding the extra template parameter(s) there.

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2022-04-11 07:03:53 UTC  
> Updated_at: 2022-04-11 07:04:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#issuecomment-1094627100  

:heavy_check_mark: renamed back to `turn_operation_type` and `get_turns`

---

## Comment 16

> Username: awulkiew  
> Created_at: 2022-04-11 14:55:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#issuecomment-1095156866  

Thanks for the fix and the discussion!  
  
Btw, would you be ok with changing `struct get_turns` to free function `detail::relate::get_turns()`? Would it be more intuitive or would you still prefer a different name to distinguish it from `geometry::get_turns()`?

---

## Comment 17

> Username: barendgehrels  
> Created_at: 2022-04-12 06:51:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#issuecomment-1096188716  

> Thanks for the fix and the discussion!  
>   
> Btw, would you be ok with changing `struct get_turns` to free function `detail::relate::get_turns()`? Would it be more intuitive or would you still prefer a different name to distinguish it from `geometry::get_turns()`?  
  
Actually the names are fine, you convinced me. ;-)  
  
A free function would be fine too, for me, unless it will depend on SFINAE.

---

## Comment 18

> Username: vissarion  
> Created_at: 2022-04-13 12:52:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/986#issuecomment-1098014364  

Sorry I was super late here! It look ok to me, though. Thanks!

---
