# #195 - Clarifying Memory Resource Base Class in Dox [Closed]

> Username: JonKalb  
> Created at: 2020-08-23 19:41:34 UTC  
> Updated at: 2020-08-24 22:00:37 UTC  
> Closed at: 2020-08-24 22:00:37 UTC  
> Url: https://github.com/boostorg/json/issues/195  

On this page: http://vinniefalco.github.io/doc/json/json/usage/allocators.html :  
  
memory_resource is listed in Table 2. Functions and Types and is also mentioned in the first paragraph of _Custom Memory Resources_.  
  
I think it would be clearer in both cases to refer to it as std::pmr::memory_resource, because it isn't clear on casual reading that this is type defined by the standard rather than by the library and I feel that this important.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-23 20:44:44 UTC  
> Url: https://github.com/boostorg/json/issues/195#issuecomment-678823011  

`memory_resource` in the docs, including this page:  
http://vinniefalco.github.io/doc/json/json/usage/allocators.html  
  
Should link to here:  
http://vinniefalco.github.io/doc/json/json/ref/boost__json__memory_resource.html
