# #31 - Bug in endpoints::brokers [Closed]

> Username: jlodos  
> Created at: 2025-05-04 05:51:08 UTC  
> Updated at: 2025-05-06 07:58:55 UTC  
> Closed at: 2025-05-06 07:58:55 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/31  

The parsing code does not correctly parse paths. For instance, parsing "mybroker.com:443/test/mqtt" will result in a path "/mqtt".  
Replacing:  
  
    auto path_ = as_<std::string>(x3::char_('/') >> *unreserved_)[to_(path)];  
  
with:  
  
    auto path_ = as_<std::string>(+(x3::char_('/') >> *unreserved_))[to_(path)];  
  
works for me.

---

## Comment 1

> Username: biljazovic  
> Created at: 2025-05-06 07:57:53 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/31#issuecomment-2853611213  

Thanks for the report!
