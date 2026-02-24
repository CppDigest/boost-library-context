# #25 - Assertion in "boost::icl::intersects" for empty interval [Open]

> Username: mkosch-uvt  
> Created at: 2019-07-17 10:56:06 UTC  
> Updated at: 2019-07-17 10:56:35 UTC  
> Url: https://github.com/boostorg/icl/issues/25  

The following example gives an assertion with boost 1.66.0 under Windows:  
```  
typedef boost::icl::discrete_interval<int,std::less> interval_t;  
typedef boost::icl::interval_set<int,std::less,interval_t> interval_set_t;  
  
interval_t int1 = interval_t::right_open(2, 7);  
interval_t int2 = interval_t::right_open(3, 3);  
interval_set_t intset;  
intset.insert(int1);  
  
// OK, returns "false":  
bool b1 = intersects(int1, int2);  
  
// Assertion in "boost::icl::non_empty::exclusive_less",  
// because interval "right" is empty:  
bool b2 = intersects(intset, int2);  
```  
Expected behavior would be that the second call to `boost::icl::intersects` also returns "false".
