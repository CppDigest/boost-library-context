# #312 - Doc for standalone should mention `memory_resource` as being required. [Closed]

> Username: hadrielk  
> Created at: 2020-09-11 11:48:20 UTC  
> Updated at: 2020-09-12 15:51:28 UTC  
> Closed at: 2020-09-12 15:51:28 UTC  
> Url: https://github.com/boostorg/json/issues/312  

Hi,  
The current doc says for standalone header mode you need C++17. While that's true, in particular it also needs `<memory_resource>` - while being part of C++17, that particular library addition was not implemented by GCC libstdc++ until version 9.1 and later.  
  
I recommend the json-lib doc mentions this specifically, because almost everything else in C++17 was implemented by GCC 7.1+, so I was surprised it failed to compile. (and my guess is `<memory_resource>` is not widely used/known)  
  
On the plus side, the generated `error` message was quite helpful and clear. :)
