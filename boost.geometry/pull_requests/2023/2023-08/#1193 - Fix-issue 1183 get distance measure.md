# #1193 Fix/issue 1183 get distance measure [Merged]

> Username: barendgehrels  
> Created at: 2023-08-30 15:55:25 UTC  
> Updated at: 2023-12-04 10:44:26 UTC  
> Merged at: 2023-09-13 18:48:58 UTC  
> Closed at: 2023-09-13 18:48:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1193  

Fixes #1183   
  
The `get_distance_measure` (which I introduced some years ago, it's not used much) is not reliable for large values and subtle changes. I have to verify other usages too.  
  
For the reported bug, where two segments are equal and the next are nearly equal, we could just use the side value (I don't remember why it was not like that earlier).  It also makes that part of the code simpler. The `verify_side` constant is not necessary either.  
  
Visual image of the problem:  
(green and blue are the two input polygons, red/green outline is union)  
It took the wrong continuation after two segments are equal.  
  
![image](https://github.com/boostorg/geometry/assets/334849/178f9c00-d48f-48b8-b02e-3eac49747698)

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2023-08-30 16:02:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1193#issuecomment-1699451415  

The added unit test for `get_distance_measure` (with a case of the fixed issue) output is below.  
For `float`, it's always wrong for these values.  
For `double`, it's wrong incidentally.   
The `side_by_triangle` and `side_robust` always agree for these points.  
  
**float**  
```  
 simplex_coll f 0 0 0 0  
 simplex_left f 0.10000002384185791016 1 1 1  
 simplex_right f -0.10000002384185791016 -1 -1 -1  
 issue_1183_p f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_q f 0 0 0 0  
 issue_1183_-5 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_-4 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_-3 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_-2 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_-1 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_0 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_1 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_2 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_3 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_4 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_5 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_6 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_7 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_8 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_9 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_10 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_11 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_12 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_13 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_14 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_15 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_16 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_17 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_18 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_19 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_20 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_250 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_251 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_252 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_253 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_254 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_255 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_256 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_257 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_258 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_259 f 16 1 -1 -1 [*** DM WRONG]  
 issue_1183_260 f 16 1 -1 -1 [*** DM WRONG]  
```  
  
**double**  
```  
 simplex_coll d 0 0 0 0  
 simplex_left d 0.099999999999999977796 1 1 1  
 simplex_right d -0.10000000000000008882 -1 -1 -1  
 issue_1183_p d 2.98023223876953125e-08 1 1 1  
 issue_1183_q d 0 0 0 0  
 issue_1183_-5 d 5.9604644775390625e-08 1 1 1  
 issue_1183_-4 d 5.9604644775390625e-08 1 1 1  
 issue_1183_-3 d 2.98023223876953125e-08 1 1 1  
 issue_1183_-2 d 2.98023223876953125e-08 1 1 1  
 issue_1183_-1 d 2.98023223876953125e-08 1 1 1  
 issue_1183_0 d 2.98023223876953125e-08 1 1 1  
 issue_1183_1 d 2.98023223876953125e-08 1 1 1  
 issue_1183_2 d 2.98023223876953125e-08 1 1 1  
 issue_1183_3 d 2.98023223876953125e-08 1 -1 -1 [*** DM WRONG]  
 issue_1183_4 d 2.98023223876953125e-08 1 -1 -1 [*** DM WRONG]  
 issue_1183_5 d 0 0 -1 -1 [*** DM WRONG]  
 issue_1183_6 d 0 0 -1 -1 [*** DM WRONG]  
 issue_1183_7 d 0 0 -1 -1 [*** DM WRONG]  
 issue_1183_8 d 0 0 -1 -1 [*** DM WRONG]  
 issue_1183_9 d 0 0 -1 -1 [*** DM WRONG]  
 issue_1183_10 d 0 0 -1 -1 [*** DM WRONG]  
 issue_1183_11 d 0 0 -1 -1 [*** DM WRONG]  
 issue_1183_12 d 0 0 -1 -1 [*** DM WRONG]  
 issue_1183_13 d -2.98023223876953125e-08 -1 -1 -1  
 issue_1183_14 d -2.98023223876953125e-08 -1 -1 -1  
 issue_1183_15 d -2.98023223876953125e-08 -1 -1 -1  
 issue_1183_16 d -2.98023223876953125e-08 -1 -1 -1  
 issue_1183_17 d -2.98023223876953125e-08 -1 -1 -1  
 issue_1183_18 d -2.98023223876953125e-08 -1 -1 -1  
 issue_1183_19 d -2.98023223876953125e-08 -1 -1 -1  
 issue_1183_20 d -2.98023223876953125e-08 -1 -1 -1  
 issue_1183_250 d 2.98023223876953125e-08 1 1 1  
 issue_1183_251 d 2.98023223876953125e-08 1 1 1  
 issue_1183_252 d 2.98023223876953125e-08 1 1 1  
 issue_1183_253 d 2.98023223876953125e-08 1 1 1  
 issue_1183_254 d 2.98023223876953125e-08 1 1 1  
 issue_1183_255 d 2.98023223876953125e-08 1 1 1  
 issue_1183_256 d 2.98023223876953125e-08 1 1 1  
 issue_1183_257 d 2.98023223876953125e-08 1 1 1  
 issue_1183_258 d 2.98023223876953125e-08 1 1 1  
 issue_1183_259 d 2.98023223876953125e-08 1 1 1  
 issue_1183_260 d 2.98023223876953125e-08 1 1 1  
```  
  
**long double**  
```  
 simplex_coll e 0 0 0 0  
 simplex_left e 0.099999999999999977796 1 1 1  
 simplex_right e -0.10000000000000008882 -1 -1 -1  
 issue_1183_p e 9.1240508481860160828e-09 1 1 1  
 issue_1183_q e 0 0 0 0  
 issue_1183_-5 e 2.7503119781613349915e-08 1 1 1  
 issue_1183_-4 e 2.3836037144064903259e-08 1 1 1  
 issue_1183_-3 e 2.0154402591288089752e-08 1 1 1  
 issue_1183_-2 e 1.6472768038511276245e-08 1 1 1  
 issue_1183_-1 e 1.280568540096282959e-08 1 1 1  
 issue_1183_0 e 9.1240508481860160828e-09 1 1 1  
 issue_1183_1 e 5.4424162954092025757e-09 1 1 1  
 issue_1183_2 e 1.7753336578607559204e-09 1 1 1  
 issue_1183_3 e -1.9063008949160575867e-09 -1 -1 -1  
 issue_1183_4 e -5.5879354476928710938e-09 -1 -1 -1  
 issue_1183_5 e -9.255018085241317749e-09 -1 -1 -1  
 issue_1183_6 e -1.2936652638018131256e-08 -1 -1 -1  
 issue_1183_7 e -1.6618287190794944763e-08 -1 -1 -1  
 issue_1183_8 e -2.0285369828343391418e-08 -1 -1 -1  
 issue_1183_9 e -2.3967004381120204926e-08 -1 -1 -1  
 issue_1183_10 e -2.7648638933897018433e-08 -1 -1 -1  
 issue_1183_11 e -3.1315721571445465088e-08 -1 -1 -1  
 issue_1183_12 e -3.4997356124222278595e-08 -1 -1 -1  
 issue_1183_13 e -3.866443876177072525e-08 -1 -1 -1  
 issue_1183_14 e -4.2346073314547538757e-08 -1 -1 -1  
 issue_1183_15 e -4.6027707867324352264e-08 -1 -1 -1  
 issue_1183_16 e -4.969479050487279892e-08 -1 -1 -1  
 issue_1183_17 e -5.3376425057649612427e-08 -1 -1 -1  
 issue_1183_18 e -5.7058059610426425934e-08 -1 -1 -1  
 issue_1183_19 e -6.0725142247974872589e-08 -1 -1 -1  
 issue_1183_20 e -6.4406776800751686096e-08 -1 -1 -1  
 issue_1183_250 e 9.1240508481860160828e-09 1 1 1  
 issue_1183_251 e 9.1240508481860160828e-09 1 1 1  
 issue_1183_252 e 9.1240508481860160828e-09 1 1 1  
 issue_1183_253 e 9.1240508481860160828e-09 1 1 1  
 issue_1183_254 e 9.1240508481860160828e-09 1 1 1  
 issue_1183_255 e 9.1240508481860160828e-09 1 1 1  
 issue_1183_256 e 9.1240508481860160828e-09 1 1 1  
 issue_1183_257 e 9.1240508481860160828e-09 1 1 1  
 issue_1183_258 e 9.1240508481860160828e-09 1 1 1  
 issue_1183_259 e 9.1240508481860160828e-09 1 1 1  
 issue_1183_260 e 9.1240508481860160828e-09 1 1 1  
```

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2023-09-13 09:51:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1193#pullrequestreview-1624152417  

Thanks!   
  
My understanding is that replacing `distance_measure` with side computation (`side_by_triange` seems to have enough accuracy for the tested use cases) resolves the issue.   
  
Is it possible that other issues can be fixed by replacing `distance_measure` everywhere in the library? Seems redundant (and also fault sensitive).

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2023-09-13 18:32:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1193#issuecomment-1718124031  

> Thanks!  
>   
> My understanding is that replacing `distance_measure` with side computation (`side_by_triange` seems to have enough accuracy for the tested use cases) resolves the issue.  
>   
> Is it possible that other issues can be fixed by replacing `distance_measure` everywhere in the library? Seems redundant (and also fault sensitive).  
  
Yes, we should deprecate this `get_distance_measure` indeed (or re-implemented using normal sides).  
Preferably using failing cases but I don't have them.  
  
I first found out that it was more precise, and in some cases it was - but in other cases, it's less precise so it shouldn't been used.  
It is relatively new so there are not yet a lot of usages.

---
