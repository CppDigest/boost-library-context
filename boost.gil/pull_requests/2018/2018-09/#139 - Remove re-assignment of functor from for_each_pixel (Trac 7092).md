# #139 Remove re-assignment of functor from for_each_pixel (Trac 7092) [Merged]

> Username: mloskot  
> Created at: 2018-09-21 16:50:13 UTC  
> Updated at: 2018-09-23 19:26:23 UTC  
> Merged at: 2018-09-23 19:26:08 UTC  
> Closed at: 2018-09-23 19:26:08 UTC  
> Url: https://github.com/boostorg/gil/pull/139  

https://svn.boost.org/trac10/ticket/7092 description:  
  
> Current implementation for for_each_pixel is somehow re-assigning the functor to its own variable.  
>   
> This becomes a serious problem when using a C++11 lambda-expression for the functor.  
>   
> Since the copy assignment operator for lambda-expressions is declared deleted by the standards, this makes it unable to pass lambda-expression for the functor of for_each_pixel. Obviously this is a serious problem for writing C++11 style codes.  
>   
> See attached patch for suggested workaround.  
  
-----  
  
The assignment was superfluous in general case and incorrect in specific case when the algorithm was given a lambda expression.  
The copy assignment operator is defined as deleted for lambda expressions.  
Add minimal test for for_each_pixel algorithm to verify it compiles with lambda expression.  
  
### Tasklist  
  
- [x] Review (@chhenning & @stefanseefeld could you review the change, please?)  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-09-23 16:18:05 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/139#pullrequestreview-157944034  

Great PR, including tests ! (Out of curiosity, did the test fail without the fix ?)

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-09-23 19:26:00 UTC  
> Url: https://github.com/boostorg/gil/pull/139#issuecomment-423841243  

@stefanseefeld Thanks. Without the fix, the test failed during compilation:  
  
```  
../../../../boost/gil/algorithm.hpp:768:17:  
  error: use of deleted function ‘test_lambda_expression()::<lambda(boost::gil::gray8_pixel_t&)>& test_lambda_expression()::<lambda(boost::gil::gray8_pixel_t&)>::operator=(const test_lambda_expression()::<lambda(boost::gil::gray8_pixel_t&)>&)’  
             fun = std::for_each(img.row_begin(y),img.row_end(y),fun);  
             ~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
for_each_pixel.cpp:21:48: note: a lambda closure type has a deleted copy assignment operator  
     gil::for_each_pixel(gil::view(image), [&sum](gil::gray8_pixel_t& p) {  
                                                ^  
```  
  
I considered making the `for_each_pixel.cpp` test a `compile` test in the `Jamfile` (and without `<boost/core/lightweight_test.hpp>` cruft), but then I realised the test program will likely be updated with run-time tests of algorithms, so I left it defined using `run` rule.

---
