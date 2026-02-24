# #1379 fix: avoid warnings for coordinate conversions and unused parameters [Merged]

> Username: barendgehrels  
> Created at: 2025-03-15 19:09:17 UTC  
> Updated at: 2025-03-18 17:18:39 UTC  
> Merged at: 2025-03-18 13:06:34 UTC  
> Closed at: 2025-03-18 13:06:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1379  

Fixes: #629  
  
This was a long standing warning where we were pinged about several times.  
  
I think this is the best solution at the moment. However, it solves one case. There is the message:  
  
>This pattern of select_most_precise<double,...> is pretty common, and I have found the same warning being produced in other parts of boost that do the same thing when using with std::int64_t.  
  
I cannot fix all of that now, it first have to wait at least for the big improvement in intersections.  
  
Then we probably should fix it in `select_most_precise` itself.  
  
Please give me your opinions.  
  
If possible it should be included in `1.88`

---

## Review 1 [Approved]

> Username: tinko92  
> Created_at: 2025-03-16 04:49:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1379#pullrequestreview-2688509115  

Looks good to me, I commented two things which are both minor. Given that you want it in 1.88 and it is a long-standing bug, I'm in favour of merging regardless of the resolution of my comments, so I selected "Approve".

📁 include/boost/geometry/strategy/cartesian/side_by_triangle.hpp

```diff
 212 |             && std::is_integral<coordinate_type_t<P>>::value;
 213 | 
 214 |+         // Promote float->double, integers to std::int64_t
```

> Username: tinko92  
> Created_at: 2025-03-16 04:35:36 UTC  
> Updated_at: 2025-03-16 04:49:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1379#discussion_r1997479880  

Is the latter part of this comment true/intended to remain true, given   
```  
// For now deactivate the use of multiprecision integers  
// TODO: activate it later  
#define BOOST_GEOMETRY_NO_MULTIPRECISION_INTEGER  
```  
<https://github.com/boostorg/geometry/blob/0900b5d61f6c0b304e78475d8e56a34cf168170d/include/boost/geometry/util/promote_integral.hpp#L14>  
 for the case in #629 where std::int64_t is used ? Or is that TODO obsolete?

> Username: barendgehrels  
> Created_at: 2025-03-16 10:08:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1379#discussion_r1997548860  

Thanks for your sharp observation.  
I will change `int64_t` in the comments (this was my first approach) because integer will go to 'long'.  
It is actually not related to `BOOST_GEOMETRY_NO_MULTIPRECISION_INTEGER` because that is really about the Boost.Multi Precision library and their integers. That is turned off indeed (I don't remember the `TODO` thanks - I think we should not turn it on automatically, it should be a conscious choice for users if they want it, or not).  
  
So what happens now (at least on my mac) is:  
`short` will to to `int`  
`int` will go to `long` (instead of `double` before - even though the comment said it would not)  
`int64` will stay `int64_t` (instead of `double` before)

> Username: barendgehrels  
> Created_at: 2025-03-16 10:35:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1379#discussion_r1997555026  

The full list (I added code to display it in a comment in #629)  
```  
t: s -> promoted_t: i  
t: i -> promoted_t: l  
t: l -> promoted_t: l  
t: x -> promoted_t: x  
t: x -> promoted_t: x  
```

> Username: tinko92  
> Created_at: 2025-03-16 11:10:32 UTC  
> Updated_at: 2025-03-16 11:11:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1379#discussion_r1997561469  

I see, thanks. My concern was, if it was a TODO to remove that macro, if it were removed then int64_t would actually be promoted to a multiprecision guide because it is in the promotion list if that macro is not defined https://github.com/boostorg/geometry/blob/0900b5d61f6c0b304e78475d8e56a34cf168170d/include/boost/geometry/util/promote_integral.hpp#L237 . I agree that it should not be turned on by default and in that case it is not a concern.

> Username: barendgehrels  
> Created_at: 2025-03-16 11:37:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1379#discussion_r1997566545  

Agreed, I changed the todo now in this PR. I didn't want to make the real change now but created another ticket #1380 to not forget it.

---

📁 include/boost/geometry/strategy/cartesian/side_by_triangle.hpp

```diff
 228 | 
 224 |-         promoted_t const zero = promoted_t();
 229 |+         static promoted_t const zero = promoted_t();
```

> Username: tinko92  
> Created_at: 2025-03-16 04:46:16 UTC  
> Updated_at: 2025-03-16 04:49:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1379#discussion_r1997481140  

Is this a microoptimisation with a meaningful benefit or is there some other intention? For primitive types (almost every case, I'd expect), I wouldn't expect it. I suppose this is for custom number types were the initialisation of 0 may be costly.  
  
I find this makes the code less clear, it made me wonder if that value was intended to be referenced/pointed to somewhere outside the scope or something like this, maybe it is just unfamiliarity on my part, though.

> Username: barendgehrels  
> Created_at: 2025-03-16 10:12:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1379#discussion_r1997549726  

It's more harmonizing with other code than a micro optimization.  
For example, in `segment_ratio.hpp`: `static thistype result(0, 1);`  
In `get_turn_info.hpp`: `static decltype(dm.measure) const zero = 0;`  
In `infinite_lien_functions.hpp`: `static Type const zero = 0;`  
  
But there are also cases where it is not static.  
I intended to make it `constexpr` instead but that is not supported (IIRC) by Boost.MultiPrecision.


---

## Comment 2

> Username: tinko92  
> Created_at: 2025-03-16 11:39:54 UTC  
> Updated_at: 2025-03-16 14:31:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1379#issuecomment-2727349378  

One more thought on this: I can see this change affects the coordinate type in the context of side-by-triangle, which evaluates a second-degree polynomial. The integer promotion promotes to an integral type with twice the digits, which will make this evaluate without integer overflow if the coordinate_type were e.g. an int32_t and the input geometry fully uses the range, the predicate would be evaluated in int64_t without overflow. For int64_t without int128 or a multiprecision type, this is not possible.  
  
I think the choice of giving floating-poitn a priority above integer in `select_most_precise` and this use in `side_by_triangle` has some merit.   
  
Converting coordinates that use the full range of `int64_t` to `double` will lose 10 bits of precision, which is fair to warn about from the compiler, and may produce an incorrect result if 1) ~~they use close to that full range of the type~~ actually if they are larger than ~2^26, that may already lead to problems and 2) they are nearly collinear.  
  
Evaluating a second degree polynomial in side by triangle in `int64_t` if the coordinates use close to the full range of the type will likely overflow and may also produce the wrong result but now silently without a warning and even if the points are not near-collinear, with undefined behaviour (signed integer overflow) and with AFAIK no standard way to detect that it happened. It will guarantee correctness for numbers with magnitude roughly in the range 2^26 - 2^31, though, unlike using `double`.  
  
So this change, exchanges a valid warning, that is only relevant if the `int64_t` coordinates are some rather large numbers (>2^26), to silently behaving worse for somewhat larger numbers (>2^31) and otherwise equivalent. Even though, the most common cases may be users with much smaller coordinates who are warned needlessly.  
  
Edit: I guess, this can also be addressed later. It shouldn't be too troubleseome to catch that case by testing the coordinates size in advance and calling a slower fall-back side by triangle for the special case of both `int64_t` and very large coordinates, using double-word multiplication. Having this extra handling to avoid overflow when promotion to double width is not possible, would allow to both avoid the warning and having good behaviour without integer overflow. Still in favor of merging.

---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2025-03-18 12:49:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1379#pullrequestreview-2694403772  

I also agree with merging.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2025-03-18 17:18:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1379#issuecomment-2734101680  

> One more thought on this  
   
> I think the choice of giving floating-poitn a priority above integer in `select_most_precise` and this use in `side_by_triangle` has some merit.  
  
Yes, going to double had some merit indeed.   
  
But I still think we should not have done it. Originally we didn’t. I don’t remember when it was changed - but the “integer” world is to be fully safe, without having FP issues in the calculations. Therefore the side should be stable, and operator on input=integer, output=integer. Same for other calculations. The intersections can fall off the grid, of course, but they are rounded.  
  
So integer should stay integer, as much as possible.  
  
And then you are right, whenever we multiply integers, we need to have the space for it. That was also discussed, long ago, with the “customer” (in this case: @vschoech). They (from Thinkcell) are aware to not use the full range of int64_t. But we should indeed document it properly. That either only part of a range can be used, or promotion should be possible and successful (for example, we could promote to Boost.Multiprecision using that define).

---
