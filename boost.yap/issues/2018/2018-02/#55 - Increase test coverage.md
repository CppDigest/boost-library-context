# #55 - Increase test coverage [Closed]

> Username: tzlaine  
> Created at: 2018-02-19 20:54:45 UTC  
> Updated at: 2018-06-11 17:16:20 UTC  
> Closed at: 2018-03-04 03:46:21 UTC  
> Url: https://github.com/boostorg/yap/issues/55  

gcov says the following are not executed by the tests  
(line numbers as of master):  
  
algorithm.hpp:  
  130 (value_expr_impl),  
  232, 246 (get_impl),  
  300 (get_c),  
  408 (callable),  
  427 (argument)  
  701 (most of op_string)  
  
expression.hpp:  
  54, 62 (value),  
  69, 77 (left),  
  84, 92 (right),  
  most unary operators (except negate)  
  most binary operators (except multiplies and plus)  
  247 (call),  
  310 (value),  
  428-420 (pre_dec, post_inc, post_dec)  
  most binary operators (except multiplies, plus, and minus)  
  
operators.hpp:  
  many unary and binary operators.  
  
print.hpp  
  42 (print_value) - Isn't this overload for  
      placeholders, rather than hana::llong?  
  50,52,54 (print_type),  
  87,89 (print_impl)  
  
detail/default_eval.hpp:  
  
  20 (eval_placeholder)  
  many unary and binary operators.

---

## Comment 1

> Username: tzlaine  
> Created at: 2018-02-25 21:01:09 UTC  
> Url: https://github.com/boostorg/yap/issues/55#issuecomment-368344163  

More coverage is now also needed for new transform() implementation that takes multiple transform objects, and transform_strict().
