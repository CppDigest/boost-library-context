# #18 - cmp should not use deduce_scalar [Closed]

> Username: agate-pris  
> Created at: 2019-08-03 10:07:34 UTC  
> Updated at: 2020-02-18 03:06:10 UTC  
> Closed at: 2020-02-18 03:06:10 UTC  
> Url: https://github.com/boostorg/qvm/issues/18  

`cmp` should not use `deduce_scalar`. `cmp` should use the function object of an argument directly.  
  
Document of `cmp` says...  
  
> Similar to `operator==`, except that the individual elements of a and b are passed  
  
but it use `deduce_scalar` and convert values.  
  
The document of `deduce_scalar` says...  
  
> This template is used by generic binary operations that return a scalar, to deduce the return type based on the (possibly different) scalars of their arguments.  
  
I think `cmp` does not need `deduce_scalar` because it does not return a scalar.  
  
I used `cmp` with `vec<double, 3>` and `vec<unsigned long long, 3>` and it cause unexpected error below.  
  
> no type named `type` in `boost::qvm::deduce_scalar_detail::deduce_scalar_impl<double, unsigned long long>` `typedef typename deduce_scalar_detail::deduce_scalar_impl<A,B>::type type;`  
  
Also `cmp` uses row array of scalar type of arguments `a` and `b`. It will not work if scalar types of that are not default constructible. And it uses `assign` to row array but it will not work if user does not include row array adaption. It is not desirable for the library itself to include it, as the user may wish not to adapt.
