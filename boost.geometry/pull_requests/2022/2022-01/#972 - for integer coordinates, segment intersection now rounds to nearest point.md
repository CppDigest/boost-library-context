# #972 [fix] for integer coordinates, segment intersection now rounds to nearest point [Merged]

> Username: barendgehrels  
> Created at: 2022-01-26 12:51:33 UTC  
> Updated at: 2022-05-22 07:30:52 UTC  
> Merged at: 2022-02-07 09:33:16 UTC  
> Closed at: 2022-02-07 09:33:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/972  

This fixes issue #930  
  
For some integer cases there were slight variations, I inspected them visual. In general it seems a bit better, and there are no real regressions (spikes might be located on different places).  
  
The best improvement is this one:  
  
old behaviour:  
![image](https://user-images.githubusercontent.com/334849/151166693-0761c2ff-aa7c-4eca-83b2-76e35a766c31.png)  
  
  
and it is now:  
![image](https://user-images.githubusercontent.com/334849/151166602-3fe6e606-7e38-4f4f-ae73-ea72aa640a00.png)

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2022-01-26 14:54:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/972#pullrequestreview-863654924  

📁 include/boost/geometry/util/math.hpp

```diff
 830 |+ {
 831 |+     return detail::divide<T>::apply(n, d);
 832 |+ }
```

> Username: awulkiew  
> Created_at: 2022-01-26 14:54:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/972#discussion_r792718835  

I'd like to propose to move away from this implementation technique (function calling struct member function) and use more modern one instead. This way there is one less function call per utility, which means smaller call trees and therefore simpler compilation error messages, maybe shorter compilation times? I'm proposing to do this in details, utilities such as this one, etc. The tags dispatching of algorithms would stay as it is. So this implementation could be replaced with:  
```  
template <typename T, std::enable_if_t<! std::is_integral<T>::value, int> = 0>  
inline T divide(T const& n, T const& d)  
{  
    return ...  
}  
  
template <typename T, std::enable_if_t<std::is_integral<T>::value, int> = 0>  
inline T divide(T const& n, T const& d)  
{  
    return ...  
}  
```

> Username: barendgehrels  
> Created_at: 2022-02-02 10:34:52 UTC  
> Updated_at: 2022-02-02 10:34:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/972#discussion_r797469750  

> I'd like to propose to move away from this implementation technique (function calling struct member function) and use more modern one instead. This way there is one less function call per utility, which means smaller call trees and therefore simpler compilation error messages, maybe shorter compilation times? I'm proposing to do this in details, utilities such as this one, etc. The tags dispatching of algorithms would stay as it is. So this implementation could be replaced with:  
>   
> ```  
> template <typename T, std::enable_if_t<! std::is_integral<T>::value, int> = 0>  
> inline T divide(T const& n, T const& d)  
> {  
>     return ...  
> }  
>   
> template <typename T, std::enable_if_t<std::is_integral<T>::value, int> = 0>  
> inline T divide(T const& n, T const& d)  
> {  
>     return ...  
> }  
> ```  
  
I don't agree with this. Our architecture is tag dispatching and specialization, we've that all over the place, and I don't feel to rebuild that all.  
This is made consistent with the other code, in the math unit - but basically everywhere.  
  
Maybe, if C++20 is there, we can make use of concepts, and we might make another choice, but that will first need some research (especially about error messages in case of errors).


---

## Comment 2

> Username: barendgehrels  
> Created_at: 2022-02-02 10:36:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/972#issuecomment-1027797928  

@vissarion OK to merge?

---

## Review 3 [Commented]

> Username: vissarion  
> Created_at: 2022-02-02 12:55:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/972#pullrequestreview-870501141  

📁 include/boost/geometry/util/math.hpp

```diff
 531 |+     static inline T apply(T const& n, T const& d)
 532 |+     {
 533 |+         return n / d;
```

> Username: vissarion  
> Created_at: 2022-02-02 12:54:45 UTC  
> Updated_at: 2022-02-02 12:55:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/972#discussion_r797575765  

what if `d==0` ?

> Username: awulkiew  
> Created_at: 2022-02-04 23:49:24 UTC  
> Updated_at: 2022-02-04 23:58:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/972#discussion_r799885519  

Then the result is as it was before so `+/-Inf` or `NaN`. But AFAIU `denominator` in segment ratio is never `0`.  
  
EDIT: actually it depends how floats are implemented, what is the architecture, etc. Plus this also catches user-defined types so it may also depend on the implementation of user-defined `operator/`. But again, this behaves like a normal division which is a replacement for.

> Username: barendgehrels  
> Created_at: 2022-02-07 09:31:28 UTC  
> Updated_at: 2022-02-07 09:31:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/972#discussion_r800464075  

Indeed, the behavior is as before.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2022-02-04 23:53:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/972#pullrequestreview-873693660  

📁 include/boost/geometry/util/math.hpp

```diff
 545 |+                    : (n + ( d + 1) / 2) / d - 1  )
 546 |+           : (d < 0 ? (n - (-d + 1) / 2) / d - 1
 547 |+                    : (n - ( d + 1) / 2) / d + 1  )
```

> Username: awulkiew  
> Created_at: 2022-02-04 23:53:49 UTC  
> Updated_at: 2022-02-04 23:54:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/972#discussion_r799887074  

More interesting is what would happen here if `d` was `0`. The answer is the same as above, i.e.: the same as before this change was made, i.e. undefined behavior.

> Username: barendgehrels  
> Created_at: 2022-02-07 09:32:51 UTC  
> Updated_at: 2022-02-07 09:32:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/972#discussion_r800465306  

I thought about it too but since the integer division lines up with the floating point division, the behavior should be the same. In other words, if we check integer divisor 0, we should also check floating point divisor 0...


---
