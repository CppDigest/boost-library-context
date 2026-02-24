# #1053 - Polygon does not cover itself when rescaling is disabled [Open]

> Username: peter-popov  
> Created at: 2022-08-16 13:50:57 UTC  
> Updated at: 2022-09-01 13:45:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1053  

Boost 1.79  
  
I am trying to evaluate relations between some polygons and multi-polygons. I found that sometimes `covered_by` returns `false` when both arguments are the same shape.  
  
Example 1:  
```  
g = "POLYGON((0.3 0.3, 0.5 0.75, 0.6 0.4, 0.3 0.3))";  
covered_by(g, g) -> false  
within(g, g) -> false  
```  
  
but   
  
Example 2:  
```  
g = "POLYGON((0 0, 1 0, 1 1, 0 1, 0 0))";  
covered_by(g, g) -> true  
within(g, g) -> true  
```  
  
Example 3:  
```  
g = "MULTIPOLYGON(((0 0, 0.0 1.0, 1.0 1.0, 1.0 0.0, 0 0), (0.1 0.1, 0.9 0.1, 0.9 0.9, 0.1 0.9, 0.1 0.1)), ((0.3 0.3, 0.5 0.75, 0.6 0.4, 0.3 0.3)), ((1.3 0.5, 1.5 1.25, 2.0 0.4, 1.5 0.1, 1.3 0.5)))";  
covered_by(g, g) -> false  
within(g, g) -> false  
```  
  
This behavior only happens when rescaling is disabled, which is the default in 1.79. And enabling rescaling causes the issue in the `is_valid()` that I have reported before in #1026.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2022-08-17 09:23:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1053#issuecomment-1217739904  

Interesting, thanks. I can reproduce it. It's related to internal calculations doing this, and there are many because you get an intersection at every node. Due to FP precision the results might differ (they do already, in my case, if I use `long double`).  
  
Both within and covered by should return `false` for all these cases.  
  
We might check `equals` for every other relational algorithm, but it takes time and we have chosen not to do that. But the user could do it himself.  
  
I believe the `equals` algorithm works correctly for all these cases.

---

## Comment 2

> Username: peter-popov  
> Created at: 2022-08-17 10:11:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/1053#issuecomment-1217813302  

Thank you for the reply.  
  
If I understand DE-9IM correctly, both within and covered should return `true`. E.g. withing is defined as `a.Within(b) ⇔ [ I(a)∩I(b)≠∅ ∧ I(a)∩E(b)=∅ ∧ B(a)∩E(b)=∅ ]`. GEOS also returns `true` in all these cases.  
  
`equals` it worked for polygons. But it does not address the case when one of the polygons is part of a MultiPolygon. For example:  
```  
g1 = MULTIPOLYGON(((0.3 0.3, 0.5 0.75, 0.6 0.4, 0.3 0.3)), ((1.3 0.5, 1.5 1.25, 2.0 0.4, 1.5 0.1, 1.3 0.5)))  
g2 = POLYGON((0.3 0.3, 0.5 0.75, 0.6 0.4, 0.3 0.3))  
within(g2, g1) -> false  
covered_by(g2, g1) -> false  
```  
  
Btw, I also tried using the `side_robust` strategy but it leads to the same result.

---

## Comment 3

> Username: vissarion  
> Created at: 2022-09-01 13:45:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/1053#issuecomment-1234303381  

Indeed within and covered_by should return `true` in all the cases above. As @barendgehrels said the issue is the FP computations used to construct intersection points. This is not related to side robustness that is why `side_robust` does not help.   
  
On the other hand, I have tried to test it with multiprecision e.g. `boost::multiprecision::cpp_dec_float_50` and although all the above examples are correct, one can create the following problematic one   
```  
g = MULTIPOLYGON(((0 0, 0.0 1.0, 1.0 1.0, 1.0 0.0, 0 0), (0.1 0.1, 0.9 0.1, 0.9 0.9, 0.1 0.9, 0.1 0.1)), ((0.3 0.3, 0.5 0.075, 0.6 0.4, 0.3 0.3)), ((1.3 0.5, 1.5 1.25, 2.0 0.4, 1.5 0.1, 1.3 0.5)))  
within(g, g) -> false  
covered_by(g, g) -> false  
```  
by simply changing one coordinate from `0.75` to `0.075` in example 3 above.   
  
This means that either there is another non numerical issue or the multiprecision types are not used internally by some computation (silently converted to FP) or something else not clear to me.
