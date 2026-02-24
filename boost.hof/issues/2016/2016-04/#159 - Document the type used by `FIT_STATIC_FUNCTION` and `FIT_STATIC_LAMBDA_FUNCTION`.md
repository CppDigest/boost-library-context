# #159 - Document the type used by `FIT_STATIC_FUNCTION` and `FIT_STATIC_LAMBDA_FUNCTION` [Open]

> Username: pfultz2  
> Created at: 2016-04-11 17:05:16 UTC  
> Updated at: 2016-04-11 17:05:16 UTC  
> Url: https://github.com/boostorg/hof/issues/159  

The type of variable created by `FIT_STATIC_FUNCTION` or `FIT_STATIC_LAMBDA_FUNCTION` could be different. Perhaps a template alias such as `static_function<F>` or `static_lambda_function<F>` can be used by the user to know the exact type of the declared variable.
