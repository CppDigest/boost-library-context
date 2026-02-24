# #34 - transparent operator like `less<void>` is not functionally usable with bimap [Open]

> Username: schaumb  
> Created at: 2022-09-13 10:51:33 UTC  
> Updated at: 2022-09-13 10:53:45 UTC  
> Url: https://github.com/boostorg/bimap/issues/34  

The current implementation is not allow to use transparent comparator/hash operator functionality.  
  
```  
boost::bimap<int, boost::bimaps::set_of<const char*, std::less<>>> test;  
  
std::string stg;  
test.right.find(stg); // error  
```  
  
The compile error message is [the following](https://godbolt.org/z/1sKaWEM3x):  
```  
/opt/compiler-explorer/libs/boost_1_80_0/boost/bimap/container_adaptor/associative_container_adaptor.hpp:194:17: error: no matching function for call to object of type 'boost::bimaps::container_adaptor::detail::key_to_base_identity<const char *, const char *const>'  
                this->template functor<key_to_base>()(k)  
```
