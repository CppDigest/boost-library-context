# #518 - boost::lexical_cast<std::string>() -> std::to_string [Closed]

> Username: octopus-prime  
> Created at: 2017-06-20 22:07:17 UTC  
> Updated at: 2017-06-21 17:03:13 UTC  
> Closed at: 2017-06-21 17:03:13 UTC  
> Url: https://github.com/boostorg/beast/issues/518  

Since we have c++11 we should use  
`  
std::to_string()   
`  
instead of   
`  
boost::lexical_cast<std::string>()  
`  
Where? In examples: port -> string

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-20 22:09:19 UTC  
> Url: https://github.com/boostorg/beast/issues/518#issuecomment-309906111  

willfix
