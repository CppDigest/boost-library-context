# #34 - support lambda/function object callbacks [Open]

> Username: nevion  
> Created at: 2015-08-16 00:08:57 UTC  
> Updated at: 2016-08-07 05:31:45 UTC  
> Url: https://github.com/boostorg/python/issues/34  

See  http://stackoverflow.com/questions/16845547/using-c11-lambda-as-accessor-function-in-boostpythons-add-property-get-sig for possible ideas and discussion  
  
We need a painless - wrapper free way to specify regular old lambdas which support captures - the plain function's yielded with +<lambda> almost satisfies this but does not support captues  
  
This causes code to be far longer than necessary and from first hand experience, lambdas are much more macro friendly.

---

## Comment 1

> Username: nevion  
> Created at: 2015-08-16 00:27:20 UTC  
> Url: https://github.com/boostorg/python/issues/34#issuecomment-131472159  

This library: http://www.boost.org/doc/libs/1_59_0/libs/function_types/doc/html/boost_functiontypes/reference/decomposition.html should shorten up get_signatures function significantly.  
  
Here is a demonstration of use with lambdas  
http://stackoverflow.com/questions/4482646/is-there-a-way-to-deduce-the-signature-of-a-lambda-as-an-mpl-sequence
