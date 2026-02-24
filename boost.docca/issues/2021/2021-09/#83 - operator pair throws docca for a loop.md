# #83 - operator pair throws docca for a loop [Open]

> Username: vinniefalco  
> Created at: 2021-09-09 04:43:14 UTC  
> Updated at: 2021-09-09 04:43:54 UTC  
> Url: https://github.com/boostorg/docca/issues/83  

```  
#include <string>  
#include <utility>  
  
struct T  
{  
    /** Return the decoded key and value as a pair  
  
        This function returns the key and value  
        as a pair, using the default allocator.  
        It is designed to allow easy copying  
        of the range into another container, as  
        shown below:  
  
        @par Example  
        @code  
        query_params_view qp = parse_query_params( "a=1&b=2&c=3&d=%34" );  
  
        std::map< std::string, std::string > m( qp.begin(), qp.end() );  
  
        // m now holds the key value pairs from the query params  
        @endcode  
    */  
    operator std::pair< std::string const, std::string >() const;  
};  
```  
  
I think this has something to do with how it calculates the name of the function:  
```Error: no ID for constraint linkend: "url.ref.boost__urls__url__params_type__value_type.operator_std__pair_lt__std__string_const,_std__string__gt_".  
Error: no ID for constraint linkend: "url.ref.boost__urls__url__params_type__value_type.operator_std__pair_lt__std__string_const,_std__string__gt_".  
Error: no ID for constraint linkend: "url.ref.boost__urls__query_params_view__value_type.operator_std__pair_lt__std__string_const,_std__string__gt_".  
```  
  
In C++ this function is called `operator std::pair< std::string const, std::string >`
