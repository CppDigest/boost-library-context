# #141 - clang-11 warning: 'allocator<void>' is deprecated [Closed]

> Username: k15tfu  
> Created at: 2021-01-28 16:37:26 UTC  
> Updated at: 2021-03-04 17:53:24 UTC  
> Closed at: 2021-03-04 17:53:24 UTC  
> Url: https://github.com/boostorg/log/issues/141  

In file boost/log/detail/allocator_traits.hpp:  
```  
boost/log/detail/allocator_traits.hpp:64:27: error: 'allocator<void>' is deprecated [-Werror,-Wdeprecated-declarations]  
struct rebind_alloc< std::allocator< void >, U >  
                          ^  
1 error generated.  
```  
  
In file boost/log/expressions/predicates/channel_severity_filter.hpp:  
```  
boost/log/expressions/predicates/channel_severity_filter.hpp:368:128: error: 'allocator<void>' is deprecated [-Werror,-Wdeprecated-declarations]  
    typedef channel_severity_filter_actor< ChannelT, SeverityT, fallback_to_none, SeverityFallbackT, less, greater_equal, std::allocator< void >, ActorT > result_type;  
                                                                                                                               ^  
```
