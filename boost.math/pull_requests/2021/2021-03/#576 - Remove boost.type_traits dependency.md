# #576 Remove boost.type_traits dependency [Merged]

> Username: mborland  
> Created at: 2021-03-21 06:17:43 UTC  
> Updated at: 2021-03-25 17:11:15 UTC  
> Merged at: 2021-03-25 08:53:03 UTC  
> Closed at: 2021-03-25 08:53:04 UTC  
> Url: https://github.com/boostorg/math/pull/576  



---

## Comment 1

> Username: mborland  
> Created_at: 2021-03-22 16:00:31 UTC  
> Url: https://github.com/boostorg/math/pull/576#issuecomment-804184363  

@jzmaddock Since all of these [failures involve Multiprecision](https://github.com/boostorg/math/pull/576/checks?check_run_id=2163312687#step:18:341) have you seen anything like it before? The fact that they're only affecting G++5 and 6 makes me think that there is a compiler/language defect that was resolved.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-03-22 19:04:54 UTC  
> Url: https://github.com/boostorg/math/pull/576#issuecomment-804318587  

>Since all of these failures involve Multiprecision have you seen anything like it before? The fact that they're only affecting G++5 and 6 makes me think that there is a compiler/language defect that was resolved.  
  
No.  But I'm guessing that the implementation is a little too cute :)  There's another issue: class `number` *always* has `real()` and `imag()` member functions (no choice really on that) so the default versions of this trait will always be true... except that there is a partial specialization here: https://github.com/boostorg/multiprecision/blob/1c8fcaea68943f86e94070a86c003513794633a7/include/boost/multiprecision/detail/number_base.hpp#L1653 which should be taken in this case, but I'm guessing is the cause of the ambiguity?  
  
The only fix I can see is to revert to using a partial specialization on std::complex for older gcc?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-03-23 08:59:41 UTC  
> Url: https://github.com/boostorg/math/pull/576#issuecomment-804734595  

Ah, a better fix might be:  
  
```  
template <class T> is_complex_type : public is_complex_type_imp {};  
```  
  
With `is_complex_type_imp` the same as your current `is_complex_type`.  That should remove the ambiguity and retain the old interface, while keeping the new functionality... I hope ;)

---

## Comment 4

> Username: mborland  
> Created_at: 2021-03-24 15:52:40 UTC  
> Url: https://github.com/boostorg/math/pull/576#issuecomment-805942887  

This is good to go.

---
