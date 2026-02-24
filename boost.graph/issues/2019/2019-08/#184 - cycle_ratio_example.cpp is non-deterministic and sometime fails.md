# #184 - cycle_ratio_example.cpp is non-deterministic and sometime fails [Open]

> Username: jzmaddock  
> Created at: 2019-08-29 09:26:36 UTC  
> Updated at: 2019-08-29 09:26:36 UTC  
> Url: https://github.com/boostorg/graph/issues/184  

We're running cycle_ratio_example.cpp in the tests, but it uses a non-deterministic random number generator which very occasionally results in the assertions failing.  It's not clear if this is inevitable, or is showing up a deeper bug of some sort.  
  
Observed failure is:  
  
```  
Vertices number: 1000  
Edges number: 30000  
Maximum cycle ratio is 9.32637  
Minimum cycle ratio is -0.45058  
Critical cycle:  
(354,386) (386,354)   
cycle_ratio_example: libs/graph/example/cycle_ratio_example.cpp:83: int main(int, char**): Assertion `std::abs(cr.first / cr.second - min_cr) < epsilon * 2' failed.  
Aborted (core dumped)  
```  
  
Actually looking at the result, I suspect we just need to up the error tolerance slightly - 2 epsilon might be a bit hopeful?
