# #204 Isomorphism improvements [Closed]

> Username: jan-grimo  
> Created at: 2020-02-13 13:29:04 UTC  
> Updated at: 2021-06-14 10:10:13 UTC  
> Closed at: 2020-02-16 10:55:21 UTC  
> Url: https://github.com/boostorg/graph/pull/204  

Closes #203 by  
- Adding doc regarding null vertices in isomorphism maps when single-vertex components are involved  
- Ignoring `max_vertex_invariant` arg and cheaply determining it in `test_isomorphism` (and adapting doc accordingly)  
- Corrects `UnaryFunction` concept requirement in doc  
- Reserves space in evaluation of all vertex invariants at start of `test_isomorphism`  
- Removes range requirements on vertex invariants, determines invariant multiplicities from existing sorted list of all invariants using `unordered_map`  
- Adds a colored isomorphism test  
- Maps unmapped vertices if isomorphism passes

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2020-02-16 06:03:41 UTC  
> Url: https://github.com/boostorg/graph/pull/204#issuecomment-586672903  

If you want each bullet point evaluated independently then it's best to make separate pull requests, otherwise it's all or nothing.  
That would also help make clear which changes are simply fixing documentation for the existing implementation versus adding to/changing the implementation.

---

## Comment 2

> Username: jan-grimo  
> Created_at: 2020-02-16 10:55:21 UTC  
> Updated_at: 2020-02-16 10:55:50 UTC  
> Url: https://github.com/boostorg/graph/pull/204#issuecomment-586692523  

Hi Jeremy.  
  
Thanks for the feedback. I've separated the points into #205 #206 and #207. Removing the range requirement on vertex invariants entirely is dependent on #206, so I will await the decision on it before creating that PR.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2020-02-24 03:57:01 UTC  
> Url: https://github.com/boostorg/graph/pull/204#issuecomment-590159807  

Thanks, I'll try to look at them soon.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-06-14 10:10:12 UTC  
> Url: https://github.com/boostorg/graph/pull/204#issuecomment-860566820  

Thanks @jargonzombies , I've been making some changes to this library, but don't claim to be any kind of Graph expert.  
  
There is/was a plan to look at this lib and all open contributions during this years C++ Now conference, @JeffGarland is that still the plan?

---
