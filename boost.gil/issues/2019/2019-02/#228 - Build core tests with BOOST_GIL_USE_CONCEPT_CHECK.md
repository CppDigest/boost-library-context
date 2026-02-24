# #228 - Build core tests with BOOST_GIL_USE_CONCEPT_CHECK [Closed]

> Username: mloskot  
> Created at: 2019-02-01 22:41:31 UTC  
> Updated at: 2019-02-05 22:19:36 UTC  
> Closed at: 2019-02-05 22:19:36 UTC  
> Url: https://github.com/boostorg/gil/issues/228  

Apparently, we have not been building any tests with the `BOOST_GIL_USE_CONCEPT_CHECK` defined,  
so no concepts being actually checked (only the spirit knows for how long).  
  
This **must** be fixed!  
  
The core tests are high priority.   
The extensions tests are lower priority.
