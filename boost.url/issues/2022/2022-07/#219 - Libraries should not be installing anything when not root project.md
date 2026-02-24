# #219 - Libraries should not be installing anything when not root project [Closed]

> Username: alandefreitas  
> Created at: 2022-07-01 17:10:28 UTC  
> Updated at: 2022-08-03 00:25:26 UTC  
> Closed at: 2022-08-03 00:25:26 UTC  
> Url: https://github.com/boostorg/url/issues/219  

Libraries should not be installing anything when not root project. (According to Peter in https://github.com/CPPAlliance/url/pull/218#discussion_r912029218)  
  
This is not limited to the case where they reside under the Boost superproject. It also applies to them being added as a subproject with add_subdirectory or FetchContent, without the Boost superproject.  
  
The library uses `NOT BOOST_SUPERPROJECT_VERSION` to identify when root project. We need to change that to `BOOST_URL_IS_ROOT`.
