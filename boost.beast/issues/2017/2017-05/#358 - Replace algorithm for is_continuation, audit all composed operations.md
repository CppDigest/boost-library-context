# #358 - Replace algorithm for is_continuation, audit all composed operations [Closed]

> Username: vinniefalco  
> Created at: 2017-05-12 22:00:11 UTC  
> Updated at: 2017-06-22 17:41:12 UTC  
> Closed at: 2017-06-22 17:41:12 UTC  
> Url: https://github.com/boostorg/beast/issues/358  

The echo_op example code introduces a leaner way to return the correct value from `is_continuation`. The novelty is using only the the step variable to calculate the return value. This eliminates the need for a default parameter to `operator()`, an additional state variable, and code to set the state variable every time in `operator()`.  
  
First we need to audit the echo_op pattern to make sure it is correct, and then apply the technique to all the composed operations in the library.  
  
- Confirmed this pattern works at the tip of v41
