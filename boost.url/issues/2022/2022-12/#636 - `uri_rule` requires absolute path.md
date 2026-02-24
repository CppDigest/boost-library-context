# #636 - `uri_rule` requires absolute path [Closed]

> Username: alandefreitas  
> Created at: 2022-12-13 23:49:02 UTC  
> Updated at: 2022-12-21 17:55:43 UTC  
> Closed at: 2022-12-21 17:55:43 UTC  
> Url: https://github.com/boostorg/url/issues/636  

The test  
  
```  
BOOST_TEST(parse_uri("http://u:p@a.b:a").has_value());  
```  
  
works but it shouldn't.  
  
It should require a `'/'` after the authority (after `:`)  
  
```  
   hier-part     = "//" authority path-abempty  
                 / path-absolute  
                 / path-rootless  
                 / path-empty  
   path-abempty  = *( "/" segment )  
```  
  
but it works because it considers ":" to be a valid `port-part` and `a` to be an independent valid path.
