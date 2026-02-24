# #17 - compile error on ms visual studio 2017(15.4.5) with /std:c++latest [Closed]

> Username: fsmoke  
> Created at: 2017-11-30 20:14:43 UTC  
> Updated at: 2017-11-30 20:25:19 UTC  
> Closed at: 2017-11-30 20:25:19 UTC  
> Url: https://github.com/boostorg/pfr/issues/17  

I have latest compiler(at this moment ) and defined BOOST_PFR_USE_CPP17, but i got error around 'size_v' in 'tuple_size.hpp' header.   
  
here:  
`template <class T>  
using flat_tuple_size = boost::pfr::detail::size_t_<decltype(boost::pfr::detail::tie_as_flat_tuple(std::declval<T&>()))::size_v>;`  
  
1>d:\consoleapplication1\3rdparty\pfr\include\boost\pfr\flat\tuple_size.hpp(26): error C2039: 'size_v': is not a member of '`global namespace''  
1>d:\consoleapplication1\3rdparty\pfr\include\boost\pfr\flat\tuple_size.hpp(26): error C2146: syntax error: missing '>' before identifier 'size_v'  
1>d:\consoleapplication1\3rdparty\pfr\include\boost\pfr\flat\tuple_size.hpp(26): error C2993: 'unknown-type': illegal type for non-type template parameter 'Index'

---

## Comment 1

> Username: fsmoke  
> Created at: 2017-11-30 20:25:19 UTC  
> Url: https://github.com/boostorg/pfr/issues/17#issuecomment-348310547  

Sorry for this issue, i read about precise support for msvc just now :(
