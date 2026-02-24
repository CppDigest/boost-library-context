# #2 fix: resolve ambiguity std::pair vs. std::tuple. Fixes ticket #9540 [Closed]

> Username: DanielaE  
> Created at: 2014-01-01 17:27:37 UTC  
> Updated at: 2016-02-25 09:20:03 UTC  
> Closed at: 2015-01-08 16:26:35 UTC  
> Url: https://github.com/boostorg/assign/pull/2  

Seen with multiple compilers which support C++11's newly introduced std::tuple. Rather than pulling just the required entities from namespace 'std', the full namespace was pulled in instead.  
  
Signed-off-by: Daniela Engert dani@ngrt.de

---

## Comment 1

> Username: Abhi43210  
> Created_at: 2016-02-25 09:20:03 UTC  
> Url: https://github.com/boostorg/assign/pull/2#issuecomment-188685672  

Hi,  
I am facing the same issue while working with "tuple". Getting ambiguity error while compiling. Ambiguity is between pulling std::tuple and boost::tuple. Need to use Boost's tuple.   
  
Any pointer or lead or solution is welcome. Exact help is expected due to urgency.

---
