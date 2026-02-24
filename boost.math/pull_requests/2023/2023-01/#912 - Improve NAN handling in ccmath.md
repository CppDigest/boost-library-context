# #912 Improve NAN handling in ccmath [Merged]

> Username: mborland  
> Created at: 2023-01-07 16:09:59 UTC  
> Updated at: 2023-01-09 15:12:32 UTC  
> Merged at: 2023-01-09 15:12:25 UTC  
> Closed at: 2023-01-09 15:12:25 UTC  
> Url: https://github.com/boostorg/math/pull/912  



---

## Review 1 [Commented]

> Username: pulver  
> Created_at: 2023-01-07 16:23:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/912#pullrequestreview-1239684858  

For the `hypot()`, `log()`, and `sqrt()` functions, shouldn't parameters with values of negative infinity result in NaN instead of negative infinity?  
  
E.g. `sqrt(-∞) = nan` rather than `-∞`.  
  
I know this behavior remains unchanged in this PR, but thought this might be an opportune time to fix.

---

## Comment 2

> Username: mborland  
> Created_at: 2023-01-07 16:31:05 UTC  
> Url: https://github.com/boostorg/math/pull/912#issuecomment-1374533938  

> For the `hypot()`, `log()`, and `sqrt()` functions, shouldn't parameters with values of negative infinity result in NaN instead of negative infinity?  
>   
> E.g. `sqrt(-∞) = nan` rather than `-∞`.  
>   
> I know this behavior remains unchanged in this PR, but thought this might be an opportune time to fix.  
  
The language specification is [+- infinity returns +- infinity](https://en.cppreference.com/w/cpp/numeric/math/hypot). Since we fallback to the STL at runtime I didn't want the edge case handling to differ based on context.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-01-07 17:30:12 UTC  
> Url: https://github.com/boostorg/math/pull/912#issuecomment-1374545726  

> The language specification is [+- infinity returns +- infinity](https://en.cppreference.com/w/cpp/numeric/math/hypot). Since we fallback to the STL at runtime I didn't want the edge case handling to differ based on context.  
  
C99 at least specifies that sqrt of a negative number is a domain error and yields a NaN, as things stand runtime sqrt(-INF) returns NaN and constexpr sqrt(-INF) returns +INF which looks just plain wrong?

---

## Comment 4

> Username: mborland  
> Created_at: 2023-01-07 17:39:40 UTC  
> Url: https://github.com/boostorg/math/pull/912#issuecomment-1374547512  

> > The language specification is [+- infinity returns +- infinity](https://en.cppreference.com/w/cpp/numeric/math/hypot). Since we fallback to the STL at runtime I didn't want the edge case handling to differ based on context.  
>   
> C99 at least specifies that sqrt of a negative number is a domain error and yields a NaN, as things stand runtime sqrt(-INF) returns NaN and constexpr sqrt(-INF) returns +INF which looks just plain wrong?  
  
Ok for `sqrt` the standard only specifies +inf. Domain error is implementation defined so NaN would make sense.

---

## Comment 5

> Username: pulver  
> Created_at: 2023-01-07 18:00:38 UTC  
> Url: https://github.com/boostorg/math/pull/912#issuecomment-1374553080  

According to https://en.cppreference.com/w/c/numeric/math/logb  
  
> If arg is ±∞, +∞ is returned  
  
I haven't tested it, but inspecting the code it appears that for the `constexpr` version  
```  
logb(-∞) = -∞  
```  
Shouldn't it return `+∞`?

---

## Comment 6

> Username: mborland  
> Created_at: 2023-01-07 18:05:25 UTC  
> Url: https://github.com/boostorg/math/pull/912#issuecomment-1374555216  

> According to https://en.cppreference.com/w/c/numeric/math/logb  
>   
> > If arg is ±∞, +∞ is returned  
>   
> I haven't tested it, but inspecting the code it appears that for the `constexpr` version  
>   
> ```  
> logb(-∞) = -∞  
> ```  
>   
> Shouldn't it return `+∞`?  
  
Yes; the change is wrong.

---

## Comment 7

> Username: mborland  
> Created_at: 2023-01-08 18:18:17 UTC  
> Url: https://github.com/boostorg/math/pull/912#issuecomment-1374896054  

@pulver Can you do one last dummy check for me? I think this is good now and addresses your `sqrt(-∞) = nan`.

---

## Review 8 [Commented]

> Username: pulver  
> Created_at: 2023-01-08 20:30:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/912#pullrequestreview-1239831333  

For `hypot()`, it looks like  
```  
hypot(-∞,1) = -∞  
```  
but should return +∞. Same when x and y are swapped, based on [hypot](https://en.cppreference.com/w/cpp/numeric/math/hypot):  
   
> if one of the arguments is ±∞, hypot(x,y) returns +∞ even if the other argument is NaN

---

## Review 9 [Approved]

> Username: pulver  
> Created_at: 2023-01-09 14:30:57 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/912#pullrequestreview-1240585260  

LGTM

---
