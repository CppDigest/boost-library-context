# #41 - bug in robust_sqrt_expr::eval2() [Open]

> Username: bubnikv  
> Created at: 2020-05-25 15:58:42 UTC  
> Updated at: 2021-03-21 14:18:30 UTC  
> Url: https://github.com/boostorg/polygon/issues/41  

The robust_sqrt_expr::eval2() is called with very long input arguments in these following two cases:  
  
```  
boost::polygon::voronoi_builder<...>::activate_circle_event(...)  
-> cfp::circle_existence_predicate<...>::operator()(...)  
	-> lazy_cff::pss(...)  
		-> mp_cff::pss(...)   
			-> rsqrexpr::eval4(eint*, eint*)  
				-> rsqrexpr::eval3(eint*, eint*)  
					-> rsqrexpr::eval2(eint*, eint*)  
			-> efpt mp_cff::sqrt_expr_evaluator_pss4<eint, efpt>(eint*, eint*)  
				-> efpt mp_cff::sqrt_expr_evaluator_pss3<eint, efpt>(eint*, eint*)  
					-> rsqrexpr::eval2(eint*, eint*)  
```  
  
where   
  
```  
using dtl = boost:polygon::voronoi::detail;  
using eint = dtl::extended_int<33ul>;  
using efpt = dtl::extended_exponent_fpt<double, dtl::extened_exponent_fpt_traits<double> >;  
using rsqrexpr = dtl::robust_sqrt_expr<eint, dtl::extended_exponent_fpt<double, dtl::extened_exponent_fpt_traits<double> >, dtl::type_converter_efpt>;  
using lazy_cff = dtl::voronoi_predicates<dtl::voronoi_ctype_traits<int> >::lazy_circle_formation_functor<dtl::site_event<int>, dtl::circle_event<double> >;  
using mp_cff = dtl::voronoi_predicates<dtl::voronoi_ctype_traits<int> >::mp_circle_formation_functor<dtl::site_event<int>, dtl::circle_event<double> >;  
using cfp = dtl::voronoi_predicates<dtl::voronoi_ctype_traits<int> >::circle_formation_predicate<dtl::site_event<int>, dtl::circle_event<double>, dtl::voronoi_predicates<dtl::voronoi_ctype_traits<int> >;  
```  
for brevity.  
  
  
The eval2() does the following operation on input arguments A, B:  
    return convert(A[0].sqr() * B[0] - A[1].sqr() * B[1]) / (a - b);  
  
Here neither the square operation nor the A[].sqr()*B[] fit the multi precision fixed point type in many cases. There is a trick inside extended_int::mul() method, which right shifts the result to fit the multi precision fixed point type, but it was not implemented correctly:  
  
1) The expression `A[0].sqr() * B[0] - A[1].sqr() * B[1]` mixes apples with oranges. Each side of the subtract operator may have been right shifted by a different exponent, therefore subtracting such two expressions does not make sense.  
  
2) The returned "float with exponent" type does not have the implicit right shift operation performed by extended_int::mul() accounted for.  
  
I would be happy to hear that I am wrong, but as of now I would bet my shoes that this code path is not correct. I have placed asserts and back trace print-outs inside the extended_int::mul() function to check for these events where the implicit right shift happens. I would only consider such operation safe, if a result of such operation was used for further multiplication followed by a signum test *(a predicate evaluation), which is certainly not the case here.  
  
I am not quite sure how to solve this. One solution is to convert the inputs of eval2() to yet wider int types. Another option is to craft eval2() to account for the right shifts inside mul(), to craft the subtract operator to account for the additional exponents of its operands etc. I may try something, though I would be thankful to @asydorchuk for his opinion. Maybe I am wrong after all and everything works correctly :-)

---

## Comment 1

> Username: bubnikv  
> Created at: 2020-05-28 15:56:41 UTC  
> Url: https://github.com/boostorg/polygon/issues/41#issuecomment-635436907  

I am providing a crude fix of the overflow in robust_sqrt_expr::eval2() by implementing   
robust_sqrt_expr::eval2ext(), which solves the overflow by normalization.  
  
https://github.com/prusa3d/polygon/commit/626bca439318c439e2d4c42f89045c32501810f8  
  
The fix is crude, as there is quite a lot of copy / paste of functions along the paths to eval2ext(). I am sure we can do better, but at least I have something to test on production data. I have also added asserts to the extended_int operators to catch for overflows, again to be tested on production data.  
  
@asydorchuk The ball is now on your side, I am now going to work on polygon offsetting using the Voronoi class. I would appreciate an example code, I believe you must have some, from which the pictures in the manual are generated :-)

---

## Comment 2

> Username: bubnikv  
> Created at: 2020-05-29 15:13:00 UTC  
> Url: https://github.com/boostorg/polygon/issues/41#issuecomment-636028193  

Frankly I am not sure what is going on, I cannot reproduce this issue anymore with the data I used to. I don't want to waste your time on it for now, though I have a feeling this issue is real and it will show up again.

---

## Comment 3

> Username: eadf  
> Created at: 2021-03-21 14:18:30 UTC  
> Url: https://github.com/boostorg/polygon/issues/41#issuecomment-803589706  

From what the code looks today it's not possible to create a ```extended_exponent_fpt``` without going through the constructors:  
```  
explicit extended_exponent_fpt(fpt_type val) {  
  val_ = std::frexp(val, &exp_);  
}  
  
extended_exponent_fpt(fpt_type val, exp_type exp) {  
  val_ = std::frexp(val, &exp_);  
  exp_ += exp;  
}  
```  
All operations performed on an ```extended_exponent_fpt``` creates a new instance.  
The constructors always normalizes the ```val_``` to ±(0.1 ≤ ```val_.abs()``` ≤ 1) (except for zero)
