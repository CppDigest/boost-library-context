# #562 - Refactor limits tests definition [Closed]

> Username: grisumbras  
> Created at: 2021-05-04 10:38:57 UTC  
> Updated at: 2021-06-28 07:14:05 UTC  
> Closed at: 2021-06-28 07:14:05 UTC  
> Url: https://github.com/boostorg/json/issues/562  

The test is currently defined in such a way that it leads to repetition (particularly in CMake). We could refactor targets' definitions to avoid that repetition.
