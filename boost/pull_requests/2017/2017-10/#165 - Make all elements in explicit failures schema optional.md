# #165 Make all elements in explicit failures schema optional [Merged]

> Username: danieljames  
> Created at: 2017-10-03 12:30:01 UTC  
> Updated at: 2017-10-09 10:29:17 UTC  
> Merged at: 2017-10-09 10:29:12 UTC  
> Closed at: 2017-10-09 10:29:12 UTC  
> Url: https://github.com/boostorg/boost/pull/165  

This is so that this schema can be used to validate module markup (such as https://github.com/boostorg/functional/blob/develop/meta/explicit-failures-markup.xml). An alternative solution would be to have a separate schema file for submodules, espcially if the reporting scripts require these elements to be present.

---
