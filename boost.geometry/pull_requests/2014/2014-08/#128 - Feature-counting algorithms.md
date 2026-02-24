# #128 Feature/counting algorithms [Merged]

> Username: mkaravel  
> Created at: 2014-08-08 11:57:49 UTC  
> Updated at: 2014-09-05 07:25:14 UTC  
> Merged at: 2014-08-14 10:57:48 UTC  
> Closed at: 2014-08-14 10:57:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/128  

This feature contains the following modifications:  
- Re-factor counting algorithms: `num_points`, `num_segments`, `num_geometries` and `num_interior_rings`: this refactoring involves moving common code to a separate file `algorithms\detail\counting.hpp` and a new namespace that I called `bg::detail::counting`. As part of the re-factoring, the `num_points` code in the `multi/algorithms` directory was also moved in the `algorithms` directory.  
- Add variant support for `num_interior_rings` and `num_geometries`.  
- Add unit tests for `num_interior_rings` and `num_geometries`.  
- Implement `num_points` and `num_segments` for boxes of any dimension: to do this I implemented the Boost.MPL-based metafunction `ipower` in the `bg::util` namespace that computes the power of an integral constant at compile time (there is also a unit test for this metafunction). As part of this change, the unit tests for `num_points` and `num_segments` now test for boxes of dimensions 3, 4 and 5.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-08-08 12:02:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#issuecomment-51592401  

One more comment about this PR: if it gets merged, then `bg::buffer` is broken as it no longer finds `bg::disjoint` (this is partly an error in the buffer code).  
PR #127 solves the issue created by this PR.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-08-08 17:29:38 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16005039  

might be inline

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-08-08 17:31:43 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16005126  

You are right. This is the unit test though, so it should not create a problem.  
I will push the change in a few seconds.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2014-08-08 17:33:26 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16005209  

Thanks for this new unit test (and others).  
As far as I can see this BOOST_AUTO_TEST_CASE usage still does not work for all compilers - will that be solved? Should we still use this? Basically I like it but I did not change my habits until that is fixed.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-08-08 17:38:08 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16005441  

@barendgehrels If I am not mistaken, if you look at the existing (few) regression tests on master, you will see that msvc-8.0 tests, that have this problem in develop, just pass.  
  
So my guess is that this is a problem with the develop branch of Boost.Typeof, and not master. In that sense I am not worried so much.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2014-08-08 17:40:57 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16005604  

The curly brace should go to next line (I know it is there on some other places too, but this is how we in general do it)

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2014-08-08 17:42:25 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16005698  

Is this typename correct here? Should it not be "inline" ?

---

## Comment 8

> Username: mkaravel  
> Created_at: 2014-08-08 17:45:10 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16005842  

Copy/paste from `num_points`. Push is on the way for `num_points`, `num_geometries` and `num_interior_rings` (all have the same issue).

---

## Comment 9

> Username: mkaravel  
> Created_at: 2014-08-08 17:46:52 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16005947  

Again artifact of copy/paste from `num_points`.  
Will push the fix shortly.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2014-08-08 18:07:54 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16007355  

either we have it on two lines   
static inline  
return_type apply  
  
or all on one line  
but please have the return type before apply (as long as it fits 80 chars)

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2014-08-08 18:08:38 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16007393  

Ehm, I believe Bruno noted that concept checking should be done after resolving variants.  
Bruno?

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2014-08-08 18:18:04 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16007943  

The open case is identical to the close case. Also for cw/ccw num_interior_rings will be identical.  
Open/close only affects num_points  
So why not combine the tests?

---

## Comment 13

> Username: mkaravel  
> Created_at: 2014-08-08 20:11:40 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16013769  

@barendgehrels In all four algorithms touched in this PR having `std::size_t apply(...)` in one line does not fit in 80 characters.  
One possibility for fixing this is to rename the argument from `geometry` to `g`, in which case `std::size_t` will fix before `apply`. Would you prefer this to what we have now?

---

## Comment 14

> Username: mkaravel  
> Created_at: 2014-08-08 20:20:52 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16014161  

You are right. Bruno had posted a comment about this when the variant support for `assign` was under discussion. I am quiting here what Bruno wrote:  
  
> Basically, concept checking can only be done once you have a concrete geometry. Apparently right now there's a specialization for variant that does nothing and thus allows the free function check to pass. I don't remember having written that so I need to check but it looks a bit hacky: check in free function, potentially doing nothing if it's a variant, and then check again in resolve_variant to have the concrete geometries properly checked if a variant was passed initially. It's better to have everything done at one place, and that's possible only once the variants (if any) are resolved. So the workflow is: resolve variant -> check concepts -> resolve default strategy -> dispatch. So this is litterally "as early as possible", at least if we don't want to hack around. I know it can make the error messages a bit less clear. Does that impact them heavily on MSVC? Regards Bruno  
  
So I will fix this according to the rule above.

---

## Comment 15

> Username: mkaravel  
> Created_at: 2014-08-08 21:44:26 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16018137  

@barendgehrels I am not 100% sure I understand what you have in mind.  
In any case I have combined the tests you mention, but I am still testing against all possible combinations of open/closed and cw/ccw.  
If you think this is too much I can simplify the tests.

---

## Comment 16

> Username: barendgehrels  
> Created_at: 2014-08-10 21:45:28 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16033569  

open/closed and w/ccw is OK. But we normally construct all of them with the same WKT (which should basically be closed to be valid...) and correct them. Therefore it is basically the same test...  
Also because you basically have the knowlegde that num_interior_rings is totally independent from this. Therefore I don't understand the purpose of all these cases (one point more, etc)...

---

## Comment 17

> Username: mkaravel  
> Created_at: 2014-08-10 21:47:10 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16033591  

@barendgehrels Okay, I get it.  
I will simplify the unit tests fir `num_interior_rings` and `num_geometries`.

---

## Comment 18

> Username: barendgehrels  
> Created_at: 2014-08-10 21:50:34 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16033601  

It is correct that it is not 4 but depends on the number of dimensions.  
But are we not trying too hard? This a compile time calculation (introducing a compiletime ipower, including a unit test). Like used for number of segments. An interesting exercise but I just question myself: are we not trying to hard to avoid one runtime calculation here?

---

## Comment 19

> Username: barendgehrels  
> Created_at: 2014-08-10 21:52:38 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16033611  

I'm still curious to the details of the need of num_segments. It is not an OGC algorithm.

---

## Comment 20

> Username: barendgehrels  
> Created_at: 2014-08-10 21:57:19 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16033634  

Why do we have the boolean flag add_for_open?  
  
For points it makes sense. We count the number of points and want to count the closing point too, optionally.   
  
Let's give an example:  
A triangle consists, as a closed polygon, of 4 points.   
As an open polygon it consists of 3 points.  
To make num_points compatble for open/closed, we added a boolean flag "add_for_open" which let the num_points function always return 4.  
  
A triangle consists, as a closed polygon, of 3 segments  
A triangle consists, as an open polygon, also of 3 segments, because it is deliberately not closed by a closing point but the segment is still there.  
Therefore I don't understand the need. I think it should go.

---

## Comment 21

> Username: mkaravel  
> Created_at: 2014-08-10 22:20:02 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16033782  

@barendgehrels Regarding your comment about `num_points` (and that it always returns 4): the default value for `add_for_open` is `false`, which means that for an open triangle you would get 3 by default, and 4 only if you set `add_for_open` to `true`. So you do not **always** get 4...  
  
Besides the above remark, stated only for completeness and nothing more, I do see your point, and I agree: there is no need for the boolean parameter. I will remove it, and adjust the implementation accordingly.

---

## Comment 22

> Username: mkaravel  
> Created_at: 2014-08-10 22:39:59 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16033902  

@barendgehrels Since dimension is specified at compile time, I thought that it would be better/appropriate to be able to also compute quantities, that depend on the dimension only, also at compile time.  
  
The truth is that the number I want to compute (for both `num_points` and `num_segments`) can be computed in a much more each way (also at compile time), so I will change the implementation accordingly (and `ipower` and its unit test will go away).

---

## Comment 23

> Username: awulkiew  
> Created_at: 2014-08-11 01:21:59 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16035048  

Well, I'm guessing that it could have some sense in the above example to return segments=2 for an open triangle and segments=3 for closed one and see the data as it is (more like a Linestring), which I'm guessing again could be in line with the num_points() implementation. Because triangle has always 3 Points but num_points() can return 3 or 4, similar situation.

---

## Comment 24

> Username: barendgehrels  
> Created_at: 2014-08-11 08:10:48 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16041454  

I don't see it like that. A triangle has always 3 segments no? The problem is not in the number of segments. That should always return 3, for a triangle (open or closed).  
The problem is in the number of points. Ideally that should return 3 too. Because a triangle obviously has 3 points. But for closed polygons (which is the OGC standard, and our default) it stores 4, and num_points according to OGC returns 4. Therefore we have to enable that option too, and it is enabled by default. Because this was the first implementation we added the add_for_open flag (which is of course optional) for open polygons. Alternatively we could have added a "remove_one_for_closed" flag, maybe that would have been better...  
  
But for a new non OGC function let's not make the same discrepancy. Rings are always closed (regardless if the model is open or close) so the number of segments should be the same for spatially the same ring.

---

## Comment 25

> Username: barendgehrels  
> Created_at: 2014-08-11 08:12:26 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16041496  

Ah, yes. No, you can then leave it as it is.

---

## Comment 26

> Username: barendgehrels  
> Created_at: 2014-08-11 08:14:21 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16041563  

OK, fine. Thnks

---

## Comment 27

> Username: barendgehrels  
> Created_at: 2014-08-11 08:15:59 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16041611  

Looks perfect ;-)

---

## Comment 28

> Username: barendgehrels  
> Created_at: 2014-08-11 08:18:17 UTC  
> Updated_at: 2014-08-13 21:57:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16041683  

Same here (looks perfect ;-) )

---

## Comment 29

> Username: mkaravel  
> Created_at: 2014-08-11 10:23:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#issuecomment-51763383  

The last three commits remove the `add_for_open` boolean argument from `num_segments`.  
  
Also, the implementation design for `num_points` has changed. `add_for_open` is passed eventually as a template parameter `AddForOpen` at the dispatch level. This way, the code for all the counting algorithms looks much more uniform and allows for better code re-use.

---

## Comment 30

> Username: barendgehrels  
> Created_at: 2014-08-11 19:26:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#issuecomment-51828158  

I'm OK with merging.  
Thanks Menelaos!

---

## Comment 31

> Username: mkaravel  
> Created_at: 2014-08-11 21:21:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#issuecomment-51842717  

Before merging I have a question. It's about the `range_count` implementation for `num_points` quoted below:  
  
```  
struct range_count  
{  
    template <typename Range>  
    static inline std::size_t apply(Range const& range)  
    {  
        std::size_t n = boost::size(range);  
        if ( n == 0 )  
        {  
            return 0;  
        }  
        if (AddForOpen  
            && geometry::closure<Range>::value == open  
            && detail::disjoint::disjoint_point_point(range::front(range),  
                                                      range::at(range, n - 1))  
            )  
        {  
            return n + 1;  
        }  
        return n;  
    }  
};  
```  
  
I see that there is a call to `disjoint` to check whether the last point of an open range matches the first point. Why do we do that?  
Suppose that I define an **open ring**  _r1_ as `POLYGON((0 0,1 0,0 1,0 0))`, and an **open ring** _r2_ as `POLYGON((0 0,1 0,0 1))`. `num_points(r1, true)` and `num_points(r2, true)` will both return 4. Isn't that a bit odd? I would think it is more natural to return 5 and 4, respectively.  
Suppose now that I convert _r1_ and _r2_ to closed, i.e., their WKT descriptions become: `POLYGON((0 0,1 0,0 1,0 0,0 0))` and `POLYGON((0 0,1 0,0 1,0 0))`, respectively. In this case `num_points(r1)` and `num_points(r2)` will return 5 and 4, respectively.  
The behavior of `num_points` I just described contradicts the current documentation which says that, if `add_to_open` is set to `true`, one is added per ring if the geometry is open (well this actually depends on how you read it, so I read it as "if the geometry is **defined** as open" and **not** as "if the geometry has its first and last points equal").  
  
So my proposal is to get rid of the check to disjoint and only check `AddForOpen` and whether the geometry is defined as open.  
  
What do you think?  
  
BTW, this piece of code is adapted from the existing implementation of `num_points` (see the develop branch), and is quoted below. So I did not invent the code quoted above, but rather just adapted it.  
  
```  
struct range_count  
{  
    template <typename Range>  
    static inline std::size_t apply(Range const& range, bool add_for_open)  
    {  
        std::size_t n = boost::size(range);  
        if (add_for_open && n > 0)  
        {  
            if (geometry::closure<Range>::value == open)  
            {  
                if (geometry::disjoint(*boost::begin(range), *(boost::begin(range) + n - 1)))  
                {  
                    return n + 1;  
                }  
            }  
        }  
        return n;  
    }  
};  
```

---

## Comment 32

> Username: mkaravel  
> Created_at: 2014-08-13 21:57:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#issuecomment-52117450  

Last three commits refer to documentation updates

---

## Comment 33

> Username: awulkiew  
> Created_at: 2014-08-14 10:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16231468  

Is this cast needed? Does it change anything? It may be changed later.

---

## Comment 34

> Username: awulkiew  
> Created_at: 2014-08-14 10:54:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#discussion_r16231813  

I'm assuming we don't care about big dimensions (>32 or 64)?  
Some time ago some user wanted to use the library for d=100.  
So in such cases the num_segments would return 0. Are we ok with this?

---

## Comment 35

> Username: barendgehrels  
> Created_at: 2014-09-04 20:08:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#issuecomment-54536755  

> So my proposal is to get rid of the check to disjoint and only check AddForOpen and whether the geometry is defined as open.  
>   
> What do you think?  
  
You are right. I agree. The check for disjoint can go.

---

## Comment 36

> Username: mkaravel  
> Created_at: 2014-09-05 07:25:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/128#issuecomment-54593150  

Done. See PR #133.

---
