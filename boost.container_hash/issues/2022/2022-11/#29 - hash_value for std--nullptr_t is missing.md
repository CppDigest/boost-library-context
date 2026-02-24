# #29 - hash_value for std::nullptr_t is missing [Closed]

> Username: pdimov  
> Created at: 2022-11-09 02:43:22 UTC  
> Updated at: 2022-11-29 00:00:48 UTC  
> Closed at: 2022-11-29 00:00:48 UTC  
> Url: https://github.com/boostorg/container_hash/issues/29  

This is needed for  
  
```  
std::size_t boost::json::hash_value( value const& jv ) noexcept  
{  
    std::size_t seed = 0;  
  
    boost::hash_combine( seed, jv.kind() == kind::int64? kind::uint64: jv.kind() );  
    boost::json::visit( [&]( auto const& v ){ boost::hash_combine( seed, v ); }, jv );  
  
    return seed;  
}  
```
