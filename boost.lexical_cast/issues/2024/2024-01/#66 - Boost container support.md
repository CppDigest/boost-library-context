# #66 - Boost container support [Closed]

> Username: serfcity  
> Created at: 2024-01-21 08:44:58 UTC  
> Updated at: 2024-01-21 11:18:39 UTC  
> Closed at: 2024-01-21 11:18:38 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/66  

Why Boost lexical_cast does not work with boost::container::string instead of std::string?

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-01-21 11:18:39 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/66#issuecomment-1902597283  

It works:  
  
https://github.com/boostorg/lexical_cast/blob/90ec909dcfe972fd8e51b9add487cbec67129b61/test/containers_test.cpp#L18-L48
