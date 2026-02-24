# #30 Fix parameters in replaced adaptor document [Merged]

> Username: nekko1119  
> Created at: 2015-04-21 17:59:45 UTC  
> Updated at: 2015-04-27 18:50:57 UTC  
> Merged at: 2015-04-27 18:50:57 UTC  
> Closed at: 2015-04-27 18:50:57 UTC  
> Url: https://github.com/boostorg/range/pull/30  

Actual implements is `replace_holder( const T& from, const T& to )`.  
But document is `ng | boost::adaptors::replaced(new_value, old_value)`.  
So, I think `new_value` and `old_value` are reversed.

---
