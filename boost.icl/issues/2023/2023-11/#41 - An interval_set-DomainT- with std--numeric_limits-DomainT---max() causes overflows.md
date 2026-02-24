# #41 - An interval_set<DomainT> with std::numeric_limits<DomainT>::max() causes overflows [Open]

> Username: michael-veksler  
> Created at: 2023-11-01 05:37:55 UTC  
> Updated at: 2023-11-01 05:37:55 UTC  
> Url: https://github.com/boostorg/icl/issues/41  

Consider https://godbolt.org/z/5xKWK9G5o  
  
namespace icl = boost::icl;  
  
```  
int main()  
{  
    icl::interval_set<uint8_t> i8;  
    i8.insert(std::numeric_limits<uint8_t>::max());  
    fmt::print("cardinality={}", cardinality(i8));  
}    
```  
this prints `cardinality=18446744073709551361` .  
The issue is the cardinality() at concept/interval.hpp which uses  `last_next(object)` which overflows to 0, in this case.  
With interval_set<int>, things would be worse, since these overflows would be undefined behavior.  
  
I suggest removing the use of `last_next` in any situation where the interval may be the biggest interval in the set.
