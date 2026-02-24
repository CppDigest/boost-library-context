# #105 - BOOST_LANG_STDCPP wrongly documentedf for C++11 [Closed]

> Username: Flamefire  
> Created at: 2020-02-27 12:24:10 UTC  
> Updated at: 2020-02-29 05:35:08 UTC  
> Closed at: 2020-02-29 05:35:08 UTC  
> Url: https://github.com/boostorg/predef/issues/105  

The docu states that the version BOOST_LANG_STDCPP of is `41.12.1`: https://github.com/boostorg/predef/blob/fb59068deb1fd1e42504c074474e0f8110b85651/include/boost/predef/language/stdcpp.h#L28  
  
But in fact it is 41.3.1 as gotten from ` __cplusplus == 201103`
