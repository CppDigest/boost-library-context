# #63 Compliant fusion::result_of::make_map changes with variadic map. [Merged]

> Username: Flast  
> Created at: 2014-10-26 08:55:34 UTC  
> Updated at: 2014-10-26 15:18:02 UTC  
> Merged at: 2014-10-26 14:06:59 UTC  
> Closed at: 2014-10-26 14:06:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/63  

fusion::result_of::make_map was changed with variadic map.  
`fusion::result_of::make_map<Key..., Value...>::type`  
v.s.  
`fusion::result_of::make_map<Key...>::apply<Value...>::type`

---
