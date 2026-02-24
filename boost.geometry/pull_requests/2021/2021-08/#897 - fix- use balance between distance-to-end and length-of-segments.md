# #897 fix: use balance between distance-to-end and length-of-segments [Merged]

> Username: barendgehrels  
> Created at: 2021-08-11 16:23:37 UTC  
> Updated at: 2021-09-13 07:27:52 UTC  
> Merged at: 2021-09-13 07:27:48 UTC  
> Closed at: 2021-09-13 07:27:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/897  

I indicated in a comment what the actual fix is.  
  
**NOTE**: several `is_valid` checks in difference tests are false negatives. I noticed already earlier some of them, but now it was in the test I concentrated on (for mysql). **We should fix that.** I explicitly skip those validity checks now, otherwise we cannot detect good enough if we're improving.  
  
This one for example:  
![uu_false_positive](https://user-images.githubusercontent.com/334849/129066321-a25cfa96-b6f9-487a-826e-55dd359f08f2.png)  
  
And this one:  
![c_false_positive_mysql](https://user-images.githubusercontent.com/334849/129066361-7564dde8-4551-4939-bf86-83dab5e7d25e.png)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-08-11 16:24:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/897#pullrequestreview-727695461  

📁 include/boost/geometry/policies/robustness/segment_ratio.hpp

```diff
 257 |+     //! Returns a value between 0.0 and 1.0
 258 |+     //! 0.0 means: exactly in the middle
 259 |+     //! 1.0 means: exactly on one of the edges (or even over it)
```

> Username: barendgehrels  
> Created_at: 2021-08-11 16:24:21 UTC  
> Updated_at: 2021-08-11 16:28:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r686988158  

This replaces `near_end` (which had a threshold). This allows more fluent behavior without thresholds.

> Username: awulkiew  
> Created_at: 2021-08-13 18:28:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r688705473  

Why is `double` returned instead of coordinate type or even better distance/comparable_distance result type (since it's compared with distances/lengths)?  
What if coordinate type and/or distance result type is more precise than double?  
Isn't `fp_type` defined for this purpose (for now ignoring the fact that it's definition is wrong, see TODO)?

> Username: barendgehrels  
> Created_at: 2021-08-18 11:11:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r691137019  

Indeed, `fp_type` was defined for this purpose, thanks. Updated.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2021-08-11 16:26:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/897#pullrequestreview-727697637  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
 206 |+             constexpr double mev = 5.0;
 207 |+             constexpr double mrcl = 1.0;
 208 |+             return mev * eva + mrcl * rcla > mev * evb + mrcl * rclb;
```

> Username: barendgehrels  
> Created_at: 2021-08-11 16:26:31 UTC  
> Updated_at: 2021-08-11 16:26:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r686989804  

This is the fix. Instead of just checking `near_end` of a and b, and if not select the smallest segment, we now have a balance: near the end gets priority (factor 5) but if they are equal, then the formula will select the smallest segment (as earlier).  
Theoretically (mathematically) the result is the same, using either a or b. But in case of numeric precision, they might be just a bit different from each other, and it can (mainly for validity checks) be important which one is chosen. Though there is also randomness involved.

> Username: vissarion  
> Created_at: 2021-08-31 11:34:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r699235057  

Why using factor 5? Are similar values also tested? Maybe add a comment in the code about that choice for future reference.

> Username: barendgehrels  
> Created_at: 2021-09-01 14:06:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r700248889  

It's basically already commented  
```  
// Prefer the segment closer to the endpoint.  
// If both are about equally close, then prefer the longer segment  
// To avoid hard thresholds, behavior is made fluent.  
// Calculate comparable length indications,  
// the longer the segment (relatively), the lower the value  
// such that the shorter lengths are evaluated higher and will  
// be preferred.  
```  
The essention is that the `mev` is much larger than `mrcl` . I'll add a comment.

> Username: barendgehrels  
> Created_at: 2021-09-01 15:10:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r700309917  

I was testing to try the effect but in the test program I used, there is no difference between `1` and `5`  
I'll come back to this.

> Username: barendgehrels  
> Created_at: 2021-09-08 09:44:26 UTC  
> Updated_at: 2021-09-08 09:44:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r704257839  

Checking this with other robustness tests, there is something wrong, there are regressions.  
Those are not regularly tested (because they are not unit tests, they work in a different way).  
  
I'm working on it and update later - it's not the balance, but it's something in the other changes for `approximately_equals`

> Username: barendgehrels  
> Created_at: 2021-09-09 09:37:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r705162322  

So the fixes are unrelated now. This PR (as at the start) fixes the balance, and the promotion type. But not yet the other-types which will follow in another commit.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2021-08-13 18:21:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/897#pullrequestreview-729868998  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
 189 |+             auto const cla = comparable_length_a();
 190 |+             auto const clb = comparable_length_b();
 191 |+             auto const clm = std::max(cla, clb);
```

> Username: awulkiew  
> Created_at: 2021-08-13 18:21:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r688701800  

This line fails with msvc. It should've been:  
```  
auto const clm = (std::max)(cla, clb);  
```

> Username: barendgehrels  
> Created_at: 2021-08-18 11:11:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r691137284  

:heavy_check_mark:


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2021-08-23 11:18:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/897#pullrequestreview-736002278  

📁 include/boost/geometry/policies/robustness/segment_ratio.hpp

```diff
 271 |-         return geometry::math::abs(m_approximation - other.m_approximation) < 50;
 270 |+         floating_point_type const result
 271 |+                 = 2.0 * std::fabs(0.5 - m_approximation / scale());
```

> Username: awulkiew  
> Created_at: 2021-08-23 11:18:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r693882130  

`geometry::math::abs` should probably still be used here because `std::fabs` may be not specialized for user-defined FP type. Also because it is used below too, so for consistency.

> Username: barendgehrels  
> Created_at: 2021-08-25 13:40:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r695763571  

:heavy_check_mark:


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2021-08-23 11:23:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/897#pullrequestreview-736006562  

📁 include/boost/geometry/policies/robustness/segment_ratio.hpp

```diff
 143 |+         <
 144 |+             std::is_floating_point<Type>::value, Type, double
 145 |+         >;
```

> Username: awulkiew  
> Created_at: 2021-08-23 11:23:53 UTC  
> Updated_at: 2021-08-23 11:29:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r693885371  

Since you moved it we may address its problem now. The problem is that user-defined FP type is going to be degraded to `double` so this typedef should rather be:  
```  
    using floating_point_type = std::conditional_t  
        <  
            std::is_integral<Type>::value, double, Type  
        >;  
```  
After this change it would not work properly for user-defined integral types but AFAIU we assume that user-defined types are FP types.  
  
In fact we should probably add traits allowing the users to specify how the library should handle user-defined numeric types but that's outside of the scope of this PR.  
  
EDIT: above in this file `std::is_integral<Type>::type::value` is checked to distinguish between numeric types so this would be consistent with it.

> Username: barendgehrels  
> Created_at: 2021-08-25 08:41:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r695533955  

Thanks for this and for your other remarks, I'm processing them.  
  
One thing: this proposed change does not work for `boost::rational` because it will set floating_point_type to rational.   
  
Your suggestion (`add traits allowing ...`)  is very good and I will use that approach (not yet making it a generic traits though)

> Username: barendgehrels  
> Created_at: 2021-08-25 09:23:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r695568983  

Or actually it will be generic. We also have `fp_coordinate_type` and `promote_floating_point` which I will combine.

> Username: awulkiew  
> Created_at: 2021-08-25 09:30:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r695574481  

Ok, though I think that they should be added consistently in the whole library which is outside of the scope of this PR. I'd be fine with a simple fix here.

> Username: barendgehrels  
> Created_at: 2021-08-25 13:23:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r695747267  

Agreed, I've something but it's growing. For now I'll apply a simple fix.

> Username: barendgehrels  
> Created_at: 2021-08-25 13:41:00 UTC  
> Updated_at: 2021-08-25 13:41:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r695763678  

:heavy_check_mark:


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2021-08-23 11:25:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/897#pullrequestreview-736007438  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
  87 |+ {
  88 |+   template <typename Ct, typename Ev>
  89 |+   static bool apply(Ct cla, Ct clb, Ev eva, Ev evb)
```

> Username: awulkiew  
> Created_at: 2021-08-23 11:25:08 UTC  
> Updated_at: 2021-08-23 11:37:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r693886053  

Shouldn't these be `const&` in case these types were user-defined to avoid copies?  
  
EDIT: After changing it you'd probably need to define `ctype` as `util::remove_cref<decltype(eva)>::type` below or simply as `Ev` because this type is known and there is no need to use `decltype` here.

> Username: barendgehrels  
> Created_at: 2021-08-25 13:41:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r695763761  

:heavy_check_mark:


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2021-08-23 11:26:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/897#pullrequestreview-736008389  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
 103 |+       using ctype = decltype(eva);
 104 |+       constexpr ctype mev = 5.0;
 105 |+       constexpr ctype mrcl = 1.0;
```

> Username: awulkiew  
> Created_at: 2021-08-23 11:26:22 UTC  
> Updated_at: 2021-08-23 11:43:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r693886824  

This will fail to compile if `Ev` is user-defined type without `constexpr` constructor implicitly taking double.  
  
EDIT: I propose to simply use `const` here as usual. The compiler will probably optimize it out anyway.  
  
EDIT2: As I mentioned above `decltype` is not needed here becasue it is known that this type is `Ev`.

> Username: barendgehrels  
> Created_at: 2021-08-25 13:41:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r695764433  

:heavy_check_mark:   
Indeed, `decltype` comes from an earlier version and is not necessary at all now. Thanks.


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2021-08-23 11:26:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/897#pullrequestreview-736008819  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
 113 |+ {
 114 |+     template <typename Ct, typename Ev>
 115 |+     static bool apply(Ct , Ct , Ev , Ev )
```

> Username: awulkiew  
> Created_at: 2021-08-23 11:26:58 UTC  
> Updated_at: 2021-08-23 11:26:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r693887099  

`const&` in case these are user-defined types.

> Username: barendgehrels  
> Created_at: 2021-08-25 13:41:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r695764546  

:heavy_check_mark:


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2021-08-25 16:04:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/897#pullrequestreview-738549111  

📁 include/boost/geometry/policies/robustness/segment_ratio.hpp

```diff
 354 |     {
 343 |-         return 1000000.0;
 355 |+         return 1000000;
```

> Username: awulkiew  
> Created_at: 2021-08-25 16:04:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r695899204  

On platforms with 16 bit int this would be incorrect. Why did you change it from `double` anyway? I thought that we assume that user-defined numeric types has to be implicitly constructible from `double` (which btw also means that they are implicitly constructible from `int`).

> Username: barendgehrels  
> Created_at: 2021-08-25 19:57:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r696069525  

- 16 bits int is promoted to double  
- I changed it because of your suggestion ;-) Because we now use user defined types i/o double, boost::multiprecision is used, and it does not accept an assignment from a double...  
- So that assumption is incorrect...  
- Maybe I should cast the const to the type first (but I assumed this was implicit here)

> Username: awulkiew  
> Created_at: 2021-08-25 21:34:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r696129572  

> 16 bits int is promoted to double  
  
I was talking about the type of the integer literal, not the type after the conversion.  
  
But it seems I was wrong. The type is not going to be `int` but the first type in which the value can fit: https://en.cppreference.com/w/cpp/language/integer_literal#The_type_of_the_literal  
  
So there shouldn't be a problem assuming that the user-defined type is implicitly constructible from an integral type capable of storing this number.  
  
> I changed it because of your suggestion  
  
Which one? :)  
  
Do you mean that this is a problem for user-defined floating point type which may not be implicitly constructible from `double`? Which type exactly this fails to compile for? I think `multiprecision::number` should be fine, the ctors are not marked as explicit. They may be disabled/enabled with type traits though. I tested your branch with `1000000.0` and the tests compile with clang-win for me.  
  
> So that assumption is incorrect...  
  
Indeed I might have been mistaken about the implicit construction. We tend to explicitly construct all numbers in the code. But if the user-defined type is not implicitly constructed from a fundamental type then it doesn't matter if this is `int` or `double`. What matters is the lack of constructor call.  
  
> Maybe I should cast the const to the type first  
  
What do you mean by "cast the const"?  
  
If the user-defined type has explicit ctor then you simply need to return `floating_point_type(1000000)` or `floating_point_type(1000000.0)`. Or am I missing something?

> Username: barendgehrels  
> Created_at: 2021-08-28 11:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r697858959  

> > I changed it because of your suggestion  
>   
> Which one? :)  
  
_"Since you moved it we may address its problem now. The problem is that user-defined FP type is going to be degraded to double so this typedef should rather be"_

> Username: barendgehrels  
> Created_at: 2021-08-28 11:44:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r697859278  

> Do you mean that this is a problem for user-defined floating point type which may not be implicitly constructible from `double`? Which type exactly this fails to compile for? I think `multiprecision::number` should be fine, the ctors are not marked as explicit. They may be disabled/enabled with type traits though. I tested your branch with `1000000.0` and the tests compile with clang-win for me.  
  
I just retested, it is not the Boost multiprecision **float** but the Boost multi precision **int** which gave the problem. I test that in the other branch. But I now realize that it's not enough, also that type should go to floating point. I will fix that later in this PR. And you are right then, this could stay as it was before.

> Username: awulkiew  
> Created_at: 2021-08-28 12:06:34 UTC  
> Updated_at: 2021-08-28 12:06:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r697861564  

So you were talking about FP typedef, not the integral literal.  
  
There are 4 issues at play here:  
1. The type `floating_point_type`  
2. whether or not it defines explicit ctor  
3. whether or not it is constructible from double  
4. the type of the literal  
  
2. and 3. may be more complex because of ctors enabled with `enable_if`.  
  
My initial remark was about the last point. I was wrong and the type is not going to be `int` but any integral type able to hold this number. So now I think that any literal you use here should be fine. This doesn't mean that this literal could be used in construction of the `floating_point_type`.  
  
I agree we shouldn't assume that the UD type can be implicitly constructible. This means that in all places ctor should be explicitly called.  
  
> I just retested, it is not the Boost multiprecision float but the Boost multi precision int which gave the problem.  
  
Ok, so this was one of the multiprecision integral types which is not "promoted" to `double` because of the change of `floating_point_type`. For this we probably needs these traits.  
  
Note that if it's not possible to create integral multipresion number from double or float then such numbers will not compile in other parts of the code where we create FP constants.

> Username: awulkiew  
> Created_at: 2021-08-28 12:07:53 UTC  
> Updated_at: 2021-08-28 12:07:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r697861686  

And once again, integer constant is fine here. So this may stay as it is now.


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2021-08-28 12:11:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/897#pullrequestreview-741021165  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
  97 |+       // Relative comparible length
  98 |+       auto const rcla = 1.0 - cla / clm;
  99 |+       auto const rclb = 1.0 - clb / clm;
```

> Username: awulkiew  
> Created_at: 2021-08-28 12:11:56 UTC  
> Updated_at: 2021-08-28 12:11:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r697862169  

So based on our discussion about the explicit creation `1.0`  should either be replaced with `Ct(1)` or a const variable as it is the case below with `mrcl`.


---

## Review 11 [Approved]

> Username: vissarion  
> Created_at: 2021-08-31 11:38:44 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/897#pullrequestreview-742612991  

I am ok to merge, I only have some questions related to some constant values in the code.

📁 include/boost/geometry/policies/robustness/segment_ratio.hpp

```diff
 348 |+     {
 349 |+         return geometry::math::abs(m_approximation - other.m_approximation)
 350 |+                 < 50;
```

> Username: vissarion  
> Created_at: 2021-08-31 11:38:06 UTC  
> Updated_at: 2021-08-31 11:38:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r699237828  

Is 50 here a magic number or is it related with some values declared in other parts of the file?

> Username: barendgehrels  
> Created_at: 2021-09-01 14:09:53 UTC  
> Updated_at: 2021-09-01 14:09:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r700251909  

It's for efficiency only, and related to the scale value 1000000.  
If it is close_to, then `operator==` uses `geometry::math::equals`, otherwise it's just false.  
  
This was already the case, the method is just moved.


---

## Review 12 [Commented]

> Username: barendgehrels  
> Created_at: 2021-09-01 15:14:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/897#pullrequestreview-744065324  

📁 include/boost/geometry/core/coordinate_promotion.hpp

```diff
  23 |+ {
  24 |+ 
  25 |+ // todo
```

> Username: barendgehrels  
> Created_at: 2021-09-01 15:14:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r700313549  

So this is the start of Adam's proposal - I didn't apply it everywhere, but the promotion is already here (below) and specialized for rational and multi precision. It should be worked out later, in another PR.  
Two related types which should be replaced are moved in this file as well.

> Username: awulkiew  
> Created_at: 2021-09-01 17:10:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r700408546  

Ok, we can build on top of it in a different PR. In case you wanted to jump into it I'll write what I think about this.  
  
I think we should add more general utilities for numeric types. So to not only cover the promotion but also type traits that could be generalized to non-fundamental types. At minimum we'd need `is_integral`/`is_floating_point` type traits and a trait allowing to define floating point type corresponding to input integer type.  
  
A name `promote_to_floating_point` seems to be ok. Another possibility is `make_floating_point` because `std::make_unsigned` and `std::make_signed` exist but I think the semantics is different so `promote` is fine. But I'd avoid past tense for sure because type traits use present tense so `make`, `remove`, `add` and therefore `promote`, `select`, `transcribe`, etc.  
  
I'm not sure about the directory. On one hand this is important and these are traits which would make `core/` the correct directory. On the other hand type selection/promotion and type_traits are already in `util/`.  
  
Anyway, these are my quick thoughts about it.

> Username: barendgehrels  
> Created_at: 2021-09-02 08:22:43 UTC  
> Updated_at: 2021-09-02 08:22:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r700862025  

Thanks, we'll come back to this.


---

## Review 13 [Commented]

> Username: barendgehrels  
> Created_at: 2021-09-09 09:40:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/897#pullrequestreview-750114196  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
 104 |+       // at all (if either a or b is used). Therefore the values of the
 105 |+       // constants are not sensitive for the majority of the situations.
 106 |+       // One known case is #mysql_23023665_6 (difference) which needs mev >= 2
```

> Username: barendgehrels  
> Created_at: 2021-09-09 09:40:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r705164230  

@vissarion this should address your concern.  
  
It tested many other robustness too (therefore you see some changes in this PR) but they are all neutral to this balance

> Username: vissarion  
> Created_at: 2021-09-09 14:31:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r705403118  

Thanks! Useful to have a specific sensitive case.


---

## Review 14 [Commented]

> Username: barendgehrels  
> Created_at: 2021-09-09 09:41:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/897#pullrequestreview-750115269  

📁 test/robustness/overlay/areal_areal/intersection_pies.cpp

```diff
  10 | #define BOOST_GEOMETRY_NO_BOOST_TEST
  11 |+ #define BOOST_GEOMETRY_NO_ROBUSTNESS
  12 |+ #define BOOST_GEOMETRY_TEST_ONLY_ONE_TYPE
```

> Username: barendgehrels  
> Created_at: 2021-09-09 09:41:09 UTC  
> Updated_at: 2021-09-09 09:41:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/897#discussion_r705165019  

The robustness tests now all test without rescaling, preceding coming change to the default


---
