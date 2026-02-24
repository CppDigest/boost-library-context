# #616 - conversion to vector of bool fails [Closed]

> Username: journeytosilius  
> Created at: 2021-08-29 22:58:34 UTC  
> Updated at: 2022-11-20 16:27:45 UTC  
> Closed at: 2022-11-20 16:27:45 UTC  
> Url: https://github.com/boostorg/json/issues/616  

```error: no matching function for call to 'value_to_impl(boost::json::value_to_tag<std::vector<bool> >, const```  
  
### Version of Boost  
  
```#define BOOST_LIB_VERSION "1_76"```  
  
### Steps necessary to reproduce the problem  
Given an json array of bool, convert to vector of bool  
```cc = value_to<vector<bool>>(jv.at(0).at("cc"));```

---

## Comment 1

> Username: pdimov  
> Created at: 2021-10-07 23:55:37 UTC  
> Url: https://github.com/boostorg/json/issues/616#issuecomment-938234581  

+1  
  
https://github.com/boostorg/json/blob/6fa272b57442074eb04c47f5a5b8bdabbb01fe0a/test/parse_into.cpp#L142-L144
