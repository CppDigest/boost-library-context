# #606 Fix of issue #596 - [buffer] fix line/line intersection point calculation for some cases, [Merged]

> Username: barendgehrels  
> Created at: 2019-06-26 12:47:23 UTC  
> Updated at: 2019-06-28 08:34:46 UTC  
> Merged at: 2019-06-27 16:09:36 UTC  
> Closed at: 2019-06-27 16:09:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/606  

now using general form (which will be used later more widely). This fixes  
some numerical issues.  
Including testcase, reported as #596

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2019-06-26 13:14:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/606#pullrequestreview-254602116  

📁 include/boost/geometry/algorithms/detail/buffer/line_line_intersection.hpp

```diff
  60 |         // they continue each other, or they form a spike
  57 |-         if (math::equals(denominator, coordinate_type()))
  61 |+         ct const zero = ct();
```

> Username: awulkiew  
> Created_at: 2019-06-26 13:14:54 UTC  
> Updated_at: 2019-06-26 13:14:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/606#discussion_r297657613  

Why not simply `zero = 0` ?

> Username: barendgehrels  
> Created_at: 2019-06-26 13:53:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/606#discussion_r297677867  

Doesn't necessarily work for user defined types.  
This is equivalent to current code.  
Thanks for your quick approvals.

> Username: awulkiew  
> Created_at: 2019-06-26 14:29:04 UTC  
> Updated_at: 2019-06-26 14:29:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/606#discussion_r297698110  

> Doesn't necessarily work for user defined types.  
  
What exactly do you have in mind? Do you have a specific example?  
  
While it would indeed not work for types to which you couldn't implicitly convert integers, we assign many other numbers in the whole library, like `1`. So the requirement is here already, user-defined types has to allow implicit initialization from integer (and double in some cases).  
  
Another possibility is that a const object cannot be created with a default ctor. But we're not talking about such case (`ct zero;`) but about explicitly assigning an integer or double (`ct zero = 0;`). Which is more readable.  
  
> This is equivalent to current code.  
  
Zero-initialization only works for POD types (https://en.cppreference.com/w/cpp/language/zero_initialization). In case of classes (type with defined constructor) the behavior is defined in the default constructor. It may as well be that the type is in uninitialized state. Why are we assuming it will be initialized to 0? So no, for user defined types this is not equivalent.

> Username: barendgehrels  
> Created_at: 2019-06-26 14:40:42 UTC  
> Updated_at: 2019-06-26 14:41:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/606#discussion_r297704739  

We did it like this from the beginning. For example here:  
```promoted_type const zero = promoted_type();``` in side_by_triangle  
```T const zero = T();``` in side_of_intersection  
and on various other places. Just looked, indeed we don't do it everywhere. So in that sense I could change it.  
Your argument for user-defined-types is a bit convoluted I think. UD types might also not support assignment and then initialization with 0 does not work. And that is (as far as I remember) the reason that we do it like it is (or was).  
  
Yes, we assign many other numbers in the library too, that is true. So (I tested mainly with ttmath) the types will always support assignment (or conversion) from numeric types.  
  
Anyway, it is equivelent to the code which I replaced, I actually meant that by my statement. Not to all current code. I could have left it there (but I find this more readable).

> Username: awulkiew  
> Created_at: 2019-06-26 15:29:40 UTC  
> Updated_at: 2019-06-26 15:29:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/606#discussion_r297731368  

> We did it like this from the beginning.  
  
Yes, I know, so my question is: why? What specific case someone wanted to solve with this? Do you remember?  
  
Is it possible that this was about some internally used simple type like `segment_ratio` which we had control on and the person adding it didn't want to implement the assignment from double because it would take a lot of time and didn't want to implement a struct template specializing for this specific type in this specific place and instead someone suggested that zero-initialization would work the same way as long as the default ctor in `ratio` type mimicked it? And since this type would be used only in some places it was needed only in a few strategies?  
  
> UD types might also not support assignment and then initialization with 0 does not work.  
  
Ok, I'll try to be more clear. I wrote about assignment from int/double it in my first point so I'm aware of that. The assignment has to work, this is our requirement. And in addition to this in some places we use zero/value-initialization which may call default ctor working in a different way than assignment. So for UDTs instead of 1 requirement we have 2 requirements:  
- assignment from integer/double,  
- default-ctor initializing to 0.  
  
> And that is (as far as I remember) the reason that we do it like it is (or was).  
  
I doubt it was for UDTs in general, unless in the very early version of the whole library there wasn't any initialization other than with 0. If you think the initialization is really an issue we should rather have special traits for initialization of UDTs. But before doing that I'd like to see a specific case or a user complain.  
  
> I tested mainly with ttmath  
  
Yes, but this was surely not for `ttmath` because:  
- ttmath::Big defines ctors taking int and double (https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/extensions/contrib/ttmath/ttmathbig.h#L3139) and assignment operators (https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/extensions/contrib/ttmath/ttmathbig.h#L3111)  
- we have modified version of ttmath anyway, probably for math constants or some operations like `math::sqrt` so we could modify the assignment if it was needed.  
  
But wait a minute, since the assignments are implemented, why do we have the whole library?  
Why not simply have traits outside ttmath?  
  
It seems that ttmath is confused about the initialization ot 0 in default constructor:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/extensions/contrib/ttmath/ttmathbig.h#L3617  
and it depends on the macro definition!  
  
And in the first version it was not there:  
https://github.com/boostorg/geometry/blob/7b1b14a5a073c6dcae9237ac75bc2f1ea59ae168/include/boost/geometry/extensions/contrib/ttmath/ttmathbig.h#L3006  
  
Does someone had to modify the default constructor in order for it to work with the library? So exactly the opposite for what you're saying.  
  
So now I'm confused, why do we zero/value-initialize in the first place if not for ttmath and ttmath was modified to accomodate for it?

> Username: barendgehrels  
> Created_at: 2019-06-26 15:42:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/606#discussion_r297737977  

Thanks for your explanations.  
  
No, I don't remember exactly the moment when we started it (it was somewhere in the beginning). I think it must still be somewhere in a really old mailing list, but you have to go back to somewhere between 2008 and 2011 I think. It was common practice at that time.  
  
I think I was the one changing ttmath (it is long ago that I touched it or compiled with it).  
  
But it was not only for ttmath - we tested more types. Yes, it is a nuisance indeed to touch external libraries.  
  
Code like this is or was necessary to use UD types, because often you cannot just use the values:  
```  
CT const delta_sigma = B * sin_sigma * (cos_2sigma_m  
  + (B/CT(4)) * ( cos_sigma * (CT(-1) + CT(2)*cos_2sigma_m_sqr)  
  - (B/CT(6) * cos_2sigma_m * (CT(-3)+CT(4)*sin_sigma_sqr)   
        * (CT(-3)+CT(4)*cos_2sigma_m_sqr)) )); // (6)  
```  
  
Anyway, I'm OK with changing behaviour of course, and ttmath is so long not tested that most code will not work for it anymore (or probably for other UDT)  
  
But for pragmatic reasons maybe we should do that if we go to C++11 (or 14) because it makes no sense to change things now and then again, because in C++11 or higher it is usually done differently.  
  
For now I think my change is acceptable because it was effectively already there in exactly the same way.

> Username: awulkiew  
> Created_at: 2019-06-26 15:51:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/606#discussion_r297742063  

I checked the original code (https://www.ttmath.org/) and the macro check in the default ctor is there as well so it was defined by the original author. Still the point remains, it is possible that UDT may initialize variable in the default ctor to a state different than value `0`.  
  
Btw, when you're testing do you have to define `TTMATH_BIG_DEFAULT_CLEAR` otherwise it fails?  
  
E.g. `IsZero()` used in comparison (https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/extensions/contrib/ttmath/ttmathbig.h#L642) only checks the` TTMATH_BIG_ZERO` and in the default ctor if `TTMATH_BIG_DEFAULT_CLEAR` is not defined only `TTMATH_BIG_NAN` flag is set.

> Username: awulkiew  
> Created_at: 2019-06-26 15:54:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/606#discussion_r297743322  

> But for pragmatic reasons  
  
Sure.  
  
> For now I think my change is acceptable because it was effectively already there in exactly the same way.  
  
Yes, thanks!

> Username: awulkiew  
> Created_at: 2019-06-26 16:00:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/606#discussion_r297746214  

> But it was not only for ttmath - we tested more types  
  
Right, we should probably do this again. At least pick some 2 most frequently used libraries and/or Boost.Multiprecision. @vissarion sometimes uses such library for accuracy testing. But I agree, not now.

> Username: vissarion  
> Created_at: 2019-06-27 12:16:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/606#discussion_r298145665  

>For now I think my change is acceptable because it was effectively already there in exactly the same way.  
  
+1  
  
>Right, we should probably do this again. At least pick some 2 most frequently used libraries and/or Boost.Multiprecision. @vissarion sometimes uses such library for accuracy testing.   
  
I was trying `ttmath` and `gmp` few years ago but with issues, I do not remember the details now but I could compile in most of the cases. Sure it would be useful to try it in the near future!

> Username: barendgehrels  
> Created_at: 2019-06-28 08:34:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/606#discussion_r298502309  

Thanks for your thoughts and approvals. It is merged now, will close the issues.


---
