# #2 Radix sort [Closed]

> Username: jeremy-murphy  
> Created at: 2014-04-13 13:43:10 UTC  
> Updated at: 2014-12-18 22:20:21 UTC  
> Closed at: 2014-12-18 22:20:21 UTC  
> Url: https://github.com/boostorg/algorithm/pull/2  

Hello team Algorithm!  
  
Radix sort has been on the list of GSoC algorithms for the past two years and I enjoy implementing algorithms so here is my (almost complete) attempt.  The early commit history is missing, I'm sorry, because I did most of the work before forking the Boost repository.  
  
Some parts of the code that I'm not 100% confident about:  
- `identity<T>`: Is this the best way to support actual unsigned integral types and types that can be implicitly converted?  
- `tr1_result_of`: Is this the right way to get result_of in this circumstance?  
- What is a trivial value of _n_?  I.e., how to test functions that might legitimately throw an exception?  
  
Obviously the tests are embarrassingly duplicated and I haven't decided how to resolve that yet.  
The only thing that is definitely incomplete is the documentation -- the code I am pretty happy with but eager to hear suggestions and criticism from others.  
  
Cheers.

---

## Comment 1

> Username: microcai  
> Created_at: 2014-04-29 16:54:57 UTC  
> Url: https://github.com/boostorg/algorithm/pull/2#issuecomment-41702102  

if only the history would have been rebased and cleand up

---
