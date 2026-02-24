# #391 - normalize incorrect for "my%3Asharona" [Closed]

> Username: vinniefalco  
> Created at: 2022-08-12 13:28:09 UTC  
> Updated at: 2022-09-15 19:48:46 UTC  
> Closed at: 2022-09-15 19:48:46 UTC  
> Url: https://github.com/boostorg/url/issues/391  

This fails:  
```  
url u( "my%3Asharona");  
u.normalize();  
assert( ! u.encoded_path().contains( ':' ) );  
```
