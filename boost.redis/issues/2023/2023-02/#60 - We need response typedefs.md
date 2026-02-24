# #60 - We need response typedefs [Closed]

> Username: mzimbres  
> Created at: 2023-02-04 08:44:51 UTC  
> Updated at: 2023-02-04 09:29:23 UTC  
> Closed at: 2023-02-04 09:29:23 UTC  
> Url: https://github.com/boostorg/redis/issues/60  

We have a `request` type but no response type. We should add  
  
`using response = std::tuple<...>;`  
  
and  
  
`using generic_response = std::vector<resp3::node<std::string>>;`  
  
The request type can be also move from `redis::resp3::request` to `redis::request`
